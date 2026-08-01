---
title: "13_Learn Claude Code ハーネスエンジニアリング学習"
tags: [summary, ai, claude-code, harness-engineering, oss, learning, mit]
source: https://x.com/DanKornas/status/2083332127546851776
repo: https://github.com/shareAI-lab/learn-claude-code
author: DanKornas (@DanKornas) / shareAI-lab
published: 2026-08-01
created: 2026-08-01
---

# Learn Claude Code —— ハーネスエンジニアリング学習

> **「コーディングエージェントがなぜ機能するのかを理解したいなら、プロンプトでなくハーネスを見る」** — エージェント環境がどう組み立てられるかを20レッスンで学ぶ、0-to-1 ハーネスエンジニアリング学習プロジェクト（MIT）。

出典: [[50_Learn Claude Code ハーネスエンジニアリング学習（出典）]]（DanKornas @DanKornas / shareAI-lab, 2026-08-01。本文はユーザー提供クリップのポストから再構成。※紹介のみ、詳細はリポジトリ要確認）

---

## 一行で

コーディングエージェントの**ハーネス（エージェント環境）がどう組み立てられるか**を学ぶ、ビルダー向け 0-to-1 学習プロジェクト（shareAI-lab/learn-claude-code・MIT）。**20の段階的レッスン**で、基本ループから豊かな機能へ、一度に1つのハーネス機構を追加しながら構築。キャッチコピーは "Bash is all you need"。

## 核心: プロンプトでなくハーネスを見る

> コーディングエージェントがなぜ機能するのかを理解したいなら、**ハーネスを見る**のが役立つ —— プロンプトだけでなく。

エージェントの挙動は プロンプト（モデルへの指示）だけで決まるのでなく、**ループ・ツール・許可・コンテキスト・操作**のハーネス機構で決まる。本プロジェクトはそのハーネスを1から構築して理解させる。

## 5領域・20レッスン

| 領域 | 内容 |
|---|---|
| **エージェントループ** | ツール呼び出しを実行し結果をモデルに返す**実行可能なループ**から開始 |
| **ツール使用** | ループを保ちつつ**ハンドラーとディスパッチマップ**を追加 |
| **許可システム** | 境界・停止条件・承認要件 |
| **コンテキストとメモリ** | スキル読み込み・コンテキスト圧縮・メモリ機構 |
| **エージェント操作** | タスクシステム・バックグラウンドタスク・チーム・**ワークツリー分離**・MCP |

## ライセンス・状況

- GitHub: shareAI-lab/learn-claude-code
- **MIT ライセンス**（OSS）
- キャッチコピー: "Bash is all you need"

> [!note] ポストが紹介のみのため詳細は推定
> ポストは OSS 紹介のみで README 転記がない。正確なレッスン構成・コード例は [GitHub リポジトリ](https://github.com/shareAI-lab/learn-claude-code) で要確認。

---

## 本ボルト内の位置付け

**ハーネスエンジニアリングの学習教材**。本ボルトの Harness/Loop/Graph 3層ノート群の「Harness 層」を具体化:

- [[02_LOOP vs GRAPH vs HARNESS ENGINEERING]]・[[08_Agent Harness vs Loop vs Graph Engineering]] が「Harness はエージェントが立つ床・必須の併用レイヤー」と概念論じるのに対し、本プロジェクトは**その Harness を20レッスンで1から構築**する実装教材
- **エージェントループ → ツール → 許可 → コンテキスト → 操作** の5領域は、[[13_Graph Architectへの20ステップ5フェーズ]]（@eng_khairallah1）の5フェーズ（Loop→グラフモデル→パターン→信頼性→判断）と対になる学習パス。前者が「概念を学ぶ」、本プロジェクトが「ハーネスを実装する」
- **許可システム（境界・停止条件・承認）** は [[14_自己レビューエージェントのGraph設計 Anthropicメソッド]]（judge・ルールブック）・[[02_24時間自走する自律型AIエージェントの設計図]]（Guardrail・安全な停止）のハーネス版
- **ワークツリー分離** は [[10_1チャットをエージェントチームへ Opus5 12ステップ]]・[[11_Graph of Loops Claude Code完全システム10リポジトリ]]・[[09_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤]] と同概念の学習実装
- **コンテキストとメモリ**（スキル読み込み・圧縮）は [[08_Context Engineering Claude Codeの文脈設計]]・[[01_Agent Skillsを作る完全プロンプト]] のハーネス統合

## 関連

- Harness の概念（3層） → [[02_LOOP vs GRAPH vs HARNESS ENGINEERING]]・[[08_Agent Harness vs Loop vs Graph Engineering]]
- 学習ロードマップ（対になる軸） → [[13_Graph Architectへの20ステップ5フェーズ]]
- 許可・judge・Guardrail → [[14_自己レビューエージェントのGraph設計 Anthropicメソッド]]・[[02_24時間自走する自律型AIエージェントの設計図]]
- ワークツリー分離 → [[10_1チャットをエージェントチームへ Opus5 12ステップ]]・[[09_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤]]
- コンテキスト・スキル → [[08_Context Engineering Claude Codeの文脈設計]]・[[01_Agent Skillsを作る完全プロンプト]]
