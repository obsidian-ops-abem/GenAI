---
title: "29_Graph of Loops Claude Code完全システム10リポジトリ"
tags: [summary, ai, graph-engineering, loop-engineering, claude-code, repos]
source: https://x.com/Granite0x/status/2080665298609328201
author: Granite0x (@Granite0x)
published: 2026-07-24
created: 2026-08-01
---

# A Graph of Loops —— Claude Code 完全システムを10リポジトリで構築

> **「グラフの各ノードはループを実行するエージェント。グラフが艦隊を調整し、ループが各ノードを信頼できるものにする」「done を取り戻せるかが全10を採点する」** — Graph と Loop の**両方を配線する**実践ガイド。

出典: [[37_Graph of Loops Claude Code完全システム10リポジトリ（出典）]]（Granite0x @Granite0x, 2026-07-24。本文はユーザー提供クリップから再構成）

---

## 一行で

「ネット上の誰もが Graph か Loop のどちらかを作るが、**両方を配線する者はいない**」—— 10個の実 GitHub リポジトリを **ソースコードレベル**で読んで、Graph（艦隊調整）+ Loop（信頼性）の二層完全システムを構築するガイド。

## 核心テーゼ

> **Build the graph out of loops you can trust — or you've just built a faster way to ship bugs across a fleet.**
> （信頼できるループでグラフを構築せよ。さもないと、艦隊全体にバグを高速出荷する手段を作ったに過ぎない）

- **Graph** は「who runs and when（誰がいつ走るか）」を決める
- **Loop** は「whether you can trust what comes back（何が戻ってきたか信頼できるか）」を決める

## The Map（10リポジトリ・2層）

### THE GRAPH（艦隊調整）

| Step | リポジトリ | 役割 | 罠 |
|---|---|---|---|
| **G1** | **bernstein** (726★) | DAG スケジューラ、モデル不使用の純Python、ワークツリー単位で `claude` CLI 起動 | 重プラットフォーム、DAGは手書き/コンパイル式 |
| **G2** | **agent-worktree** (267★) | エージェントごとに git worktree、**dry-run マージ**で衝突時は巻き戻し | スケジューラでなくプリミティブ（G1の仕事） |
| **G3** | **wshobson/agents** (38k★) | 94プラグイン・**203の専門サブエージェント**、役割ごと Opus/Haiku ティア | 全203を context 入れると窓を燃やす |
| **G4** | **insane-research** (108★) | 出荷済み7フェーズ研究グラフ、Phase6が**コードゲート**（モデルでなく `validate_ledger.py` が検証） | validate スキップで synthesis が空に |

### THE LOOP（1ノードの信頼性）

| Step | リポジトリ | 役割 | 罠 |
|---|---|---|---|
| **L1** | **beads** (25k★) | SQL上の依存グラフ記憶、`bd prime` が**約50トークン**で再注入、古い作業は70%小さいstubへ | .beads/ を iCloud/Dropbox に置くと DB 破損 |
| **L2** | **waku-agent** (440★) | **最小ループ**（agent.py 約95行）「This file is the whole trick.」doneフラグなし、`range(1, max_iter+1)` デフォルト10 | ツールエラーを文字列に飲み込み正常に見える（L5が存在理由） |
| **L3** | **serena** (26k★) | Claude Code の**編集不能~16kトークン**を計測、シンボルレベル取得を強制 | 検証はしない、強いリファクタは有料JetBrains必要 |
| **L4** | **superpowers** (260k★) | TDDスキル「Iron Law: NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST」 | 説得であって syscall ではない（強制はL5） |
| **L5** | **claude-review-loop** (706★) | Stop hook、最大4レビュアー並列（Codex）が `reviews/review-<id>.md` を要求、fail open 設計 | LICENSEなし、レビューファイル存在のみチェック |
| **L6** | **workshop** (937★) | 実トレースを編集後コードで再駆動しツール呼び出しをdiff、spans緑まで再実行 | 本番ハンドラを指すと本物DB/課金/メールを叩く |

## 重要な数字

> Claude Code はあなたが1文字打つ前に、**見えも編集もできないビルトインツール説明で約16,000トークン**を消費する。**L3（serena）がそれを取り戻す。**

## 全体の組み立て（Assemble It）

> bernstein が DAG にコンパイルしノードを spawn → agent-worktree が隔離ツリーを与える → wshobson/agents が役割を与える。
> 各ノード内側でループが走る: beads が記憶し、waku が反復し、serena が触れるコードだけを与え、superpowers が手を与え、claude-review-loop が第2モデル署名まで終了をブロックし、workshop が証明する。
> insane-research が今晚走らせる全体形: ファンアウト→コード検証→統合。

## 金言: 「done を取り戻せるか」

> **One test grades all ten: can your system take done back?**

- bernstein はゲート失敗ノードのマージ拒否
- beads はタスクを not-ready に戻す
- review hook は完了セッションを未完了に戻す
- workshop は緑トレースを fail させる

> **A system that can only promote is a burndown chart with extra steps.**（昇格しかできないシステムは、余分なステップ付きのバーンダウンチャート）
> **Draw the graph. But build it out of loops first.**（グラフを描け。だが、まずループで構築せよ）

---

## 本ボルト内の位置付け

本ボルトの「○○ Engineering」系譜において **Graph と Loop の統合**を明示した独自ノート:

| 既存ノートの軸 | 本ノートの位置 |
|---|---|
| [[21_...]]・[[06_...]] 3層並列比較 | **Graph+Loop の実装統合**（Harness は G1/L3 等） |
| [[23_...]] Loop↔Chain | **Graph 層を Loop の上にどう構築するか** |
| [[24_...]] サブエージェント | G2 worktree・G3 役割分割の具体実装 |
| [[25_...]] Loop↔Graph判断 | 「まず Loop（L1-L6）で信頼できるノードを作ってから Graph（G1-G4）」と整合 |
| [[26_...]] ファクトリ | 「done を取り戻せるか」= no evals no production / ゲート思想と同一 |

特に「**done を取り戻せるか**」は [[26_...]] の「no evals, no production」や [[23_...]]「接地した検証器」と同じ根拠ベース停止の原則。10リポジトリの実装例は、抽象論を実コードへ落とす参照として非常に実用的。

## 関連

- Graph+Loop 二層 → [[21_Agent Harness vs Loop vs Graph Engineering]]・[[06_LOOP vs GRAPH vs HARNESS ENGINEERING]]
- Loop 信頼性・ゲート → [[23_Loop Engineering Claude,GPT 実戦で効くもの]]・[[26_エージェントファクトリの作り方 ビルダーズガイド]]
- worktree・ファンアウト → [[24_1チャットをエージェントチームへ Opus5 12ステップ]]
- 「done を取り戻せる」= 根拠停止 → [[27_24時間自走する自律型AIエージェントの設計図]]
- Graph は必要時だけ → [[25_Graph Engineering 最大の間違い Loop↔Graph判断]]・[[19_Graph Engineering Clearly Explained]]
