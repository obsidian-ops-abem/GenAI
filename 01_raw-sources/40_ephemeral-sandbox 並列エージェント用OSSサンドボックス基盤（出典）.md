---
title: "40_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤（出典）"
tags: [raw-source]
source: https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox
repo: https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox
author: Ephemeral-AI-Lab
created: 2026-08-01
---

# 出典メタデータ

- GitHub: https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox
- Website: https://ephemeral-sandbox.com/
- Docs: https://ephemeral-sandbox.com/docs
- 著者: Ephemeral-AI-Lab
- 形態: GitHub README（OSS 製品紹介）
- ライセンス: **MIT License**
- 実装: Rust（ヘッドレスコア）。ブラウザ UI は別リポジトリ [Ephemeral Sandbox Console](https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox-console)
- 外部テスト・ベンチマーク: https://github.com/Ephemeral-AI-Lab/ephemeral-sandbox-test

要約は [[09_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤]] を参照。

> [!info] 本文取得について
> 本記事はユーザーが Clippings フォルダへクリップした README 全文を提供したため、それに基づき転記。

---

# 本文（再構成）

## What is Ephemeral Sandbox?

**Open-source agent sandbox infrastructure for parallel coding agents.** 同じコードベースに対して並列にコーディングエージェントを実行するための OSS 基盤。各エージェントは1つの共有サンドボックス内の**隔離されたワークスペースセッション**を通じて作業し、レビュー可能な変更セットを公開する。

マルチエージェントシステム向けのエージェントサンドボックス基盤として、**基盤となるサンドボックスを複製せず、エージェントを1つのミュータブルなチェックアウトに押し込めることなく**、各コーディングエージェントに隔離ワークスペースセッションを与える。

本リポジトリはヘッドレスな Rust コア（gateway / manager / daemon / runtime / observability / CLI / MCP）を含む。ブラウザ UI とそのバックエンドは別リポジトリ。

## Why use an ephemeral sandbox for coding agents?

- **Work in parallel.** 複数のコーディングエージェントを同時に実行
- **Stay isolated.** 1つの安定したプロジェクトベース上に、各エージェントにプライベートな書き込み可能ワークスペースセッションを提供
- **Scale an agent sandbox.** 1つの共有サンドボックス内でエージェント群を動かしつつ、各コーディングエージェントは自身のワークスペースセッションを保持
- **Publish with confidence.** 完全に解決済みの変更セットを公開する前に、アクティビティと変更の出所を検査

> [!warning] セキュリティモデルの位置付け
> Ephemeral Sandbox は**協調するコーディングエージェント向けのワークスペース隔離**を提供する。**相互に信頼できないテナント向けの堅牢な microVM 境界ではない**。デプロイ境界の選択前に [agent sandbox security model](https://ephemeral-sandbox.com/agent-sandbox-security) を読むこと。

## Quick start

Docker がインストール済み・到達可能であること。ホスト OS 別のバイナリをダウンロードし、gateway を起動。

- **Linux amd64**: `bin/start-sandbox-linux-docker-gateway`
- **macOS arm64**: `bin/start-sandbox-macos-docker-gateway`
- **Windows amd64**: PowerShell で `bin\start-sandbox-windows-docker-gateway.ps1`
- **Console/Desktop UI**: gateway 起動後 `127.0.0.1:7880` でブラウザ UI（別リポジトリ）

## Choose an interface

| Interface | Best for | Start with |
|---|---|---|
| **CLI** | オペレータ・スクリプト・ローカル開発 | `sandbox-manager-cli help` |
| **MCP** | コーディングエージェント・MCP 互換クライアント | `bin/setup-codex-mcp` |

CLI と MCP は **3つのツールグループ** を使う:

- **Management** —— サンドボックスの作成・検査・エクスポート・破棄
- **Runtime** —— サンドボックス内でコマンド実行・ファイル読み書き
- **Observability** —— ヘルス・イベント・リソース・ファイルシステムレイヤの検査

各 MCP サーバは1つのツールグループを公開: `sandbox-mcp --set management|runtime|observability`

## How it works

1. **Share a stable base.** LayerStack がプロジェクト履歴を全ワークスペースセッションで利用可能に保つ
2. **Work in isolation.** 各エージェントに独自の書き込み可能ワークスペースと実行境界を与える
3. **Publish safely.** 完全に解決済みの変更セットを公開する前に並行変更を検査し、さもなくば何も公開しない

設計の深掘りは [architecture overview](https://ephemeral-sandbox.com/architecture)。
