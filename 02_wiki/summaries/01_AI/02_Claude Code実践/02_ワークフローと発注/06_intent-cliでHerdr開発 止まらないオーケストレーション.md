---
title: "06_intent-cliでHerdr開発 止まらないオーケストレーション"
tags: [summary, ai, workflow, intent-cli, herdr, agmsg, orchestration, multi-agent]
source: https://zenn.dev/jtechjapan_pub/articles/intent-cli-herdr-orchestration
author: 高丘 @tomohisa（株式会社ジェイテックジャパン）
published: 2026-08-03
created: 2026-08-03
---

# intent-cli で Herdr 開発 — 止まらないオーケストレーションの工夫

> **AIエージェント4スレッド（設計・オーケストレーション・実装・レビュー）を GitHub Issue/PR 経由で協調させる運用で、agmsg から herdr-only モードへ移行。「誰が turn を起こすか」を再設計し、起こす経路を3段冗長化（完了報告/状態変化/定期点検）した実践記。**

出典: [[71_intent-cliでHerdr開発 止まらないオーケストレーション（出典）]]（@tomohisa, 2026-08-03。本文はクリップ全文を使用。コンセプト編/実践編/Loop Engineering編 の続編）

---

## 一行で

@tomohisa による intent-system（intent-cli）の運用改善記。4スレッド協調のメッセージ駆動化で止まりを検出・再開する仕組みを設計。agmsg の「受信側常駐」という故障点を減らすため herdr-only モードを作り、最初の実仕事で全員 idle 停止に直面。起こす経路の3段冗長化で解決した。[[05_herdr+agmsgでintent-cli開発 スレッド分離オーケストレーション]]（前身）の「なぜ止まらないか」の設計思想。

## 4スレッド構成（核心）

| スレッド | 役割 | 人間の入口 |
|---|---|---|
| **設計** | 意図をまとめ、ADR を残し、packet と受け入れ基準を決める | **唯一の人間入口** |
| **オーケストレーション** | packet を Issue に切り出し（1回1件）、CI/レビュー完了待ち、マージ・closeout。判断せず進行役 | — |
| **実装** | Issue を受けて実装し PR 作成。見るのは Issue/PR とコードだけ | — |
| **レビュー** | PR を Issue/packet/意図/ADR と照合 | — |

人間に上がるのは「意図の曖昧さ・受け入れ基準の欠落・セキュリティ/破壊的操作」だけ。実績：4か月で作業単位1,537件（完了1,531件）、マージPR約1,460件。

## 核心問題：「turn を起こすメッセージ」

コーディングエージェントは **turn（発話）が来て初めて動く**。1回の turn を終えると次に何かが turn を作るまで待つだけ。

agmsg の monitor が受信メッセージを turn に変換していたが、2つの教訓：
1. **黙って完了してはいけない** — 報告なき完了は「失われた仕事」になる（88分放置の実例）
2. **publish と委譲は同じ turn 内でやりきる** — 後回しにすると次の turn が作れない（60時間停止の実例）

## herdr-only 移行の動機

agmsg の codex bridge がコードの約27%（約2,900行）を占め、受信側常駐が故障点だった。herdr の `agent prompt <ペイン>` なら**どのスレッドの turn でも観測側から起こせる**。受信側 monitor 常駐が不要に。

agmsg モード（既定/PRIMARY）と herdr-only モード（プレビュー）を両方残し、1チーム1モード（混在不可・配送壊れる）。

## agmsg 外した直後の停止と3段冗長化

herdr-only 初仕事で3ロール全員 idle 停止。agmsg の完了報告経路が消え、完了が自分のペインにしか現れなかった。**起こす経路を1本に頼らず冗長に**：

1. **一次（完了報告で起こす）**：worker が `intent-cli notify report` でオーケストレーションのペインを prompt。情報量最多。
2. **二次（状態変化で起こす）**：herdr socket API `events.subscribe` で `pane.agent_status_changed` 監視。`working` → settled（idle/done/blocked）の瞬間だけ起こす。settle delay とロール単位重複排除付き。※成否を運ばないので起きた後に `herdr pane read --source recent-unwrapped` で確認。
3. **最後の網（定期点検）**：`intent-cli automation stalled-work` で止まっている作業を洗い出し（承認済み未マージ PR 等）。

基準：**「どの単一の経路が落ちても停止は検出できる状態を保つ」**。

## 最重要原則：状態 ≠ 成功

**状態が変わったことは、作業が成功したことを意味しない**。ペインが idle でも承認待ちかもしれない。完了判定は次を合わせて確認：settle 状態 + 承認/質問で止まっていない + 成果物が実在し検証通る + 最新の intent-cli/GitHub の事実と一致。

[[05_マージゲート4シグナル 信頼スコアの罠とレーン分離]] の「エージェントが影響できるのは信頼スコア1つ」と同じ問題意識。[[02_Graph Engineering 最大の間違い Loop↔Graph判断]] の「状態だけで判定しない」とも共鳴。

## 止まりの多くは「承認プロンプト」

`rm -rf /tmp/...`・PR作成・PRコメント追加の承認で頻発停止。

方針：**オーケストレーションは承認を勝手に押さない**。認証/セキュリティ/権限/破壊的操作/設計判断は常に人間へエスカレーション。「セッションを進めることと、代わりに判断することは別」。対策：専用 worktree 置き場（`.intent-cli/worktrees/`）だけを `git worktree remove` 対象に。

## 衝突防止の実運用工夫

事故から追加したものを含む：
- ロールごとに作業場所（独立クローン/worktree）を分ける（フォルダ共有で片方が静かに受信停止した事故）
- メタデータは `main-metadata`、コードは `main`（PR一覧がメタデータ変更で埋まらない）
- WIP を1件に絞る（同時多重防止）
- `git add -A` 禁止、push 直前に `git pull --ff-only`（他チームの先 push を push 失敗で検出）
- アイドル時 push 禁止（空コミットで草が生い茂る）
- **自己申告は生きている証明にならない**（起動報告直後に基盤が落ちて全滅し、設計スレッドが「起動待ち」のままだった事故）
- 共有プロセス消去前に所有境界確認（別プロジェクトの codex app-server を kill して他チームを巻き添え）

## サブエージェントを「設計スレッドに頼むだけ」で立てる

設計スレッドへの依頼はモデル指定だけで完結：

```
intent-cli の最新の機能を使って、herdr-only のチームを作ってください。
orchestrator  codex sol high
implement     codex sol middle
reviewer      claude opus 5
```

手順もコマンドも書かない。設計スレッドが intent-cli に現在のやり方を聞き、herdr 上にペインを立て、エージェントを起動、役割を記録してチームを組み上げる。

## intent-cli と herdr の役割分担

- **herdr**：ペインへの prompt 送信と状態観測を担う（トランスポート層）
- **intent-cli**：宛先のロール対応・委譲と報告の形式・モード記録・止まり点検・設計境界イベント記録という「決めごと」を生成・検証（規約層）。**intent-cli 自身は AI を起動しない**。エージェントが intent-cli に聞き、正規手順で進む

## 関連

- [[05_herdr+agmsgでintent-cli開発 スレッド分離オーケストレーション]] — 前身。スレッド分離の最初の実例。本記事はその「止まらない」ための再設計
- [[06_herdrを使いこなす エージェント連携と自作プラグイン]] — herdr の機能面の網羅的マニュアル。本記事の CLI 群（`agent prompt`/`agent wait`/`events.subscribe`）の詳細
- [[01_intent-system 意図駆動開発のオーケストレーションCLI]] — intent-cli v0.6.0 の紹介
- [[03_ループ仕様 論文 コーディングエージェントの実行レイヤ]] — 「turn が来ないと動かない」の学術的定式化。明示的トリガー/検証可能目標/停止ルール/メモリ

## 所感

「速く回す」は珍しくない。難しいのは「止まったのを見つけて再開させ続ける」こと。起こす経路の冗長化と「状態≠成功」の原則は、長時間自律運用（[[01_ループエンジニアリング14ステップ]]/[[02_Loop Engineering Claude,GPT 実戦で効くもの]]）の実装パターンとして汎用。マルチマシン対応（依存を減らした動機の将来像）は未実装。
