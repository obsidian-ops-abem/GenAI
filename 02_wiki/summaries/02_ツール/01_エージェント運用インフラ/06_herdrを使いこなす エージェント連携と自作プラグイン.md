---
title: "06_herdrを使いこなす エージェント連携と自作プラグイン"
tags: [summary, ai, tool, herdr, multi-agent, tmux, plugin, orchestration]
source: https://blog.techscore.com/entry/2026/08/03/080000
author: techscore（シナジーマーケティング株式会社）
published: 2026-08-03
created: 2026-08-03
---

# herdr を使いこなす — 複数 AI エージェントの連携から自作プラグインまで

> **herdr（サーバー常駐型ターミナルワークスペース管理ツール）の基本操作から、複数エージェント連携・完了待ち自動化・セッション永続化・自作プラグインまでを実例解説。tmux ライクの操作感で、エージェントの状態（working/idle/blocked/done）をサイドバーで一覧できる。**

出典: [[70_herdrを使いこなす 複数AIエージェント連携と自作プラグイン（出典）]]（techscore, 2026-08-03。本文はクリップ全文を使用。herdr v0.7.5 時点）

---

## 一行で

techscore による herdr の網羅的チュートリアル。インストール・基本操作から、lead/helper パターンのエージェント連携、CLI つなぎ合わせの自動化、プラグイン自作（キー起動型・イベント反応型）までをカバー。[[05_herdr+agmsgでintent-cli開発 スレッド分離オーケストレーション]] で触れられた herdr の操作詳細を補完する実践マニュアル。

## herdr の位置づけ

herdr は「AI エージェントを動かすために作られた」サーバー常駐型ターミナルマルチプレクサ。tmux ライクだが、AI エージェントの状態検出・サイドバー可視化・エージェント間プロンプト送信を独自機能として持つ。1つのバイナリで動き、設定は `config.toml` 1つ。

## 用語体系（核心）

| 用語 | 意味 |
|---|---|---|
| サーバー/クライアント | サーバー常駐型。クライアント（画面）を閉じてもサーバー側でエージェントは動き続ける |
| セッション | 作業全体のまとまり。名前を付けて複数持てる（`default`/名前付き） |
| ワークスペース | プロジェクト単位の作業空間（サイドバーの「spaces」） |
| タブ → ペイン | ワークスペース内の画面切り替え単位 → ペイン内の画面分割（1ペイン=1端末） |
| エージェント | ペインの中で動く AI コーディングエージェント（Claude Code/Codex 等） |

## エージェント状態検出（herdr の独自性）

サイドバーが各ペインのエージェント状態を自動検出・色分け表示：

| 状態 | 意味 |
|---|---|
| `working` | 処理中 |
| `blocked` | 承認/質問 UI が出て人の応答待ち |
| `idle` | 入力待ち（タブ表示済み・状況把握済み） |
| `done` | 入力待ちだが完了をまだ見ていない（タブをフォーカスすると idle に変わる） |

`idle` と `done` の違いは「自分がまだ結果を確認していないかどうか」だけ。裏で終わった作業のうち未確認のものだけが `done` として残る。

## エージェント間プロンプト送信

`herdr agent prompt <pane_id> "<テキスト>"` で、別ペインのエージェントに直接プロンプト送信・サブミット。人間のコピペ往復を不要にする。

**エージェント自身が herdr CLI を叩ける** ため、「左のペインのエージェントにこの方針を伝えて」と自然言語で頼むだけで実行される。コマンドを暗記する必要がない。

## lead/helper パターン（エージェントに段取りを任せる）

- **lead**（司令塔役）：公式 [Agent skill](https://herdr.dev/docs/agent-skill/) を読み込み、herdr の操作を自分の判断で行う。**用意するのは lead 1体だけ**。
- **helper**（手伝い役）：lead が `herdr pane split` + `herdr agent start` で自分で作る、ふつうのエージェント。特別な準備不要。

lead に「lint・test・docs を別エージェントに分担させて、全部終わったらまとめて」と頼むと、lead が helper を3つ立て、それぞれ指示し、`herdr agent wait` で待ち合わせ、結果を集約する。分担の粒度変更はスクリプト書き直しでなく lead への指示変更で済む。

## 完了待ちの自動化（CLI つなぎ合わせ）

```bash
# 実装完了を待ってテスト起動、失敗したら通知
herdr agent wait "$IMPL"
herdr pane run "$TEST" "sh -c 'npm test && echo DONE'"
herdr pane wait-output "$TEST" --match DONE || herdr notification show "test failed"
```

人間はペインに張り付かず、通知が来たときだけ確認すればよい。

## セッション永続化・リモート

- デタッチ `prefix+q` → 再接続 `herdr`
- 別マシンから `herdr --remote <ssh-target>`
- 再起動後も復元 `resume_agents_on_restore = true`（既定で有効）

## 自作プラグイン（実行ファイル＋マニフェストだけ）

SDK 不要。スクリプト＋`herdr-plugin.toml`（＋任意でキー割当）の3部品。

**キー起動型**：`[[actions]]` で宣言。例：ディレクトリを選んで workspace を作る `open-dir`（zoxide/ghq を候補取得元にし、fzf が無ければ純 bash にフォールバック）。

**イベント反応型**：`[[events]]` で購読。例：`pane.agent_status_changed` で blocked/done になったペインへ自動フォーカス。`HERDR_PLUGIN_EVENT_JSON` でイベント内容（`agent_status`/`workspace_id`/`pane_id`）を受け取る。

GitHub に置けば `herdr plugin install <owner>/<repo>` で配布可能。

## 関連

- [[05_herdr+agmsgでintent-cli開発 スレッド分離オーケストレーション]] — 本記事の herdr 操作を使った intent-cli 開発の実運用例。本記事が herdr の機能面、あちらが運用・オーケストレーション設計面
- [[06_intent-cliでHerdr開発 止まらないオーケストレーション]] — 同著者(@tomohisa)の herdr-only モード移行話。本記事の CLI 群があちらの wake 経路（完了報告/状態変化/定期点検）の実装詳細
- [[02_agmsg CLIエージェント間メッセージング]] — herdr の `agent prompt` と機能が重複する agmsg。herdr-only 移行の対象

## 所感

herdr は「与えられた機能を使うツール」でなく「自分で拡張していくツール」。本体機能の穴（任意フォルダの workspace 作成等）も数十行のシェルスクリプトで埋める設計思想。AI エージェントの状態検出とサイドバー可視化が、tmux との最大の差別化。[[01_Vibe Kanban コーディングエージェント用Kanbanワークスペース]] や [[04_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤]] と並ぶエージェント運用インフラの選択肢。
