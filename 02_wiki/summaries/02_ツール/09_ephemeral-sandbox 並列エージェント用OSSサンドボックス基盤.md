---
title: "09_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤"
tags: [summary, tool, coding-agent, sandbox, parallel-agents, rust, mcp]
source: https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox
author: Ephemeral-AI-Lab
created: 2026-08-01
---

# ephemeral-sandbox —— 並列エージェント用 OSS サンドボックス基盤

> **「1つの共有サンドボックスを複製せず、エージェントを1つのミュータブルなチェックアウトに押し込めず、各エージェントに隔離ワークスペースを与える」** — 並列コーディングエージェント向けのワークスペース隔離インフラ（Rust コア・MIT）。

出典: [[40_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤（出典）]]（Ephemeral-AI-Lab。本文はユーザー提供クリップの README 全文から再構成）

---

## 一行で

同じコードベースに対し**複数のコーディングエージェントを並列実行**するための OSS 基盤。**1つの共有サンドボックス**内で各エージェントに**隔離された書き込み可能ワークスペースセッション**を与え、完了後にレビュー可能な変更セットを**原子的に公開**する。Rust ヘッドレスコア＋CLI/MCP。

## 解く問題

複数のコーディングエージェントを並列に動かすとき:

- 同じチェックアウトを **ミュータブルに共有** すると衝突する
- サンドボックスを **エージェント数だけ複製** すると重い
- 各エージェントの作業内容・変更の出所が **追跡不能** になる

README はこれを **4つの "Why"** で表明する:

| Why | 内容 |
|---|---|
| **Work in parallel** | 複数のコーディングエージェントを同時に実行 |
| **Stay isolated** | 1つの安定したプロジェクトベース上に、各エージェントへプライベートな書き込み可能ワークスペースセッションを提供 |
| **Scale an agent sandbox** | 1つの共有サンドボックス内でエージェント群を動かしつつ、各エージェントは自身のワークスペースセッションを保持 |
| **Publish with confidence** | 完全に解決済みの変更セットを公開する前に、アクティビティと**変更の出所（change provenance）**を検査 |

→ 「ベースは共有・書き込みは隔離・公開は原子的」で解く。

## 3層の仕組み（How it works）

| 層 | 役割 |
|---|---|
| **Share a stable base** | LayerStack がプロジェクト履歴を全ワークスペースセッションで利用可能に保持 |
| **Work in isolation** | 各エージェントに独自の書き込み可能ワークスペース＋実行境界を付与 |
| **Publish safely** | 完全に解決済みの変更セット公開前に並行変更を検査、さもなくば**何も公開しない**（atomic） |

## インターフェース（CLI / MCP、3ツールグループ）

| Interface | Best for | 起動 |
|---|---|---|
| **CLI** | オペレータ・スクリプト・ローカル開発 | `sandbox-manager-cli help` |
| **MCP** | コーディングエージェント・MCP 互換クライアント | `bin/setup-codex-mcp` |

3つのツールグループ:

- **Management** —— サンドボックスの作成・検査・エクスポート・破棄
- **Runtime** —— サンドボックス内でコマンド実行・ファイル読み書き
- **Observability** —— ヘルス・イベント・リソース・**ファイルシステムレイヤ**（LayerStack の積層状態）の検査

> [!note] Observability と「状態の外部保存」
> 3ツール群のうち **Observability** が独立したグループとして存在する点が重要。並列エージェントが「どのワークスペースで何をしたか」をイベント・ファイルシステムレイヤとして検査可能＝**状態をエージェントの頭の中でなく外部に保存**する設計。これは本ボルトの `index.md`/`log.md` による状態外部保存や、[[02_24時間自走する自律型AIエージェントの設計図]] の「壊れても原因を残し再開できる」思想と同じ系譜。

各 MCP サーバは1グループを公開（`sandbox-mcp --set management|runtime|observability`）。ブラウザ UI は別リポジトリ（Ephemeral Sandbox Console）。

> [!warning] セキュリティモデルの位置付け
> **協調するコーディングエージェント向けのワークスペース隔離**を提供するものであり、**相互に信頼できないテナント向けの堅牢な microVM 境界ではない**。デプロイ境界の選択前に [agent sandbox security model](https://ephemeral-sandbox.com/agent-sandbox-security) を読むこと。

## ライセンス・状況

- **MIT License**（OSS）
- 実装: **Rust** ヘッドレスコア（gateway / manager / daemon / runtime / observability / CLI / MCP）
- GitHub: Ephemeral-AI-Lab/ephemeral-sandbox
- 前提: **Docker** がインストール済み・到達可能（Linux/macOS/Windows 各バイナリ配布）

---

## 本ボルト内の位置付け

エージェント並列実行の**インフラ（隔離）層**。[[05_Vibe Kanban コーディングエージェント用Kanbanワークスペース]]（エージェント並列の**人間側UI**・差分レビュー）や [[06_agmsg CLIエージェント間メッセージング]]（エージェント間の**通信**）が「エージェント同士／人間とエージェント」のインターフェースなら、ephemeral-sandbox は**エージェントが安全に並列で書き込む土俵**を提供する。

特に「ワークスペース隔離」の概念は [[10_1チャットをエージェントチームへ Opus5 12ステップ]]（worktree 隔離）や [[11_Graph of Loops Claude Code完全システム10リポジトリ]]（G2 agent-worktree）と同系統だが、本作は**製品としてのインフラ基盤**（gateway/daemon/observability を含むヘッドレスコア）を提供する点が異なる。「atomic publication（全部解けるか、さもなくば無）」は [[02_24時間自走する自律型AIエージェントの設計図]]（Guardrail 層・安全な停止）の思想と通底。

## 関連

- エージェント並列のワークスペース分離 → [[10_1チャットをエージェントチームへ Opus5 12ステップ]]・[[11_Graph of Loops Claude Code完全システム10リポジトリ]]
- エージェント並列の周辺（UI・通信） → [[05_Vibe Kanban コーディングエージェント用Kanbanワークスペース]]・[[06_agmsg CLIエージェント間メッセージング]]
- 安全な停止・原子性 → [[02_24時間自走する自律型AIエージェントの設計図]]
