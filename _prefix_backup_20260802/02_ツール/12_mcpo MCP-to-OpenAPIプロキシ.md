---
title: "12_mcpo MCP-to-OpenAPIプロキシ"
tags: [summary, tool, mcp, openapi, proxy, http, oss, mit]
source: https://x.com/DanKornas/status/2083309467798544836
repo: https://github.com/open-webui/mcpo
author: DanKornas (@DanKornas) / open-webui
published: 2026-08-01
created: 2026-08-01
---

# mcpo —— MCP-to-OpenAPI プロキシ

> **「Many MCP tools are awkward to connect to apps built around HTTP and OpenAPI」** — MCP サーバを HTTP/OpenAPI の背後に置き、OpenAPI スキーマとインタラクティブドキュメントを自動生成するプロキシ。

出典: [[51_mcpo MCP-to-OpenAPIプロキシ（出典）]]（DanKornas @DanKornas / open-webui, 2026-08-01。本文はユーザー提供クリップのポストから再構成）

---

## 一行で

**MCP サーバを OpenAPI 互換アプリ/エージェントで利用できるようにするプロキシ**（open-webui/mcpo・MIT）。MCP サーバのコマンド/エンドポイントを取り、**OpenAPI スキーマとインタラクティブドキュメントを生成**して HTTP インターフェースの背後に置く。stdio・SSE・Streamable HTTP に対応し、Claude Desktop 形式の設定でマルチサーバも可、OAuth 2.1 認証もサポート。

## 解く問題

多くの MCP ツールは、**HTTP と OpenAPI を中心に構築されたアプリ**に接続するのが不格好。MCP（Model Context Protocol）と OpenAPI（HTTP REST の標準スキーマ）はプロトコルの前提が異なり、直接繋げない。mcpo がその**橋渡し（プロキシ）**を担う。

## 5つの特徴

| 特徴 | 内容 |
|---|---|
| **OpenAPI プロキシ** | MCP ツールを標準 HTTP エンドポイントで公開 |
| **インタラクティブドキュメント** | 各ツールのドキュメント UI を自動生成 |
| **トランスポートサポート** | **stdio・SSE・Streamable HTTP** の MCP サーバに対応 |
| **マルチサーバ設定** | Claude Desktop 形式の設定ファイルから複数 MCP ツールを提供 |
| **OAuth 2.1** | 保護された Streamable HTTP MCP サーバの認証をサポート |

## ライセンス・状況

- GitHub: open-webui/mcpo（open-webui は Open WebUI の運営組織）
- **MIT ライセンス**（OSS）

> [!note] ポストが紹介のみのため詳細は推定
> ポストは OSS 紹介のみで README 転記がない。正確な使い方・設定例は [GitHub リポジトリ](https://github.com/open-webui/mcpo) で要確認。

---

## 本ボルト内の位置付け

**MCP の相互運用性インフラ**。本ボルトの MCP 系ノート群を補完:

- [[02_Claude Code開発者ボリス推奨 MCPサーバー8選]]（Boris Cherny の3原則・Context7/Playwright/Figma 等の公式 MCP サーバー8選）が「どの MCP サーバを使うか」なら、mcpo は **MCP サーバを OpenAPI アプリへどう繋ぐか**のインフラ
- **「OpenAPI スキーマ自動生成」** は、[[03_Graph Engineering with Claude 14-Step roadmap]]（@0xCodez step03 node 契約・JSON schema で validate）や [[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]（judge・境界ある入出力）の「契約（schema）」思想のインフラ版。MCP ツールの境界を OpenAPI スキーマで明示
- **Claude Desktop 形式のマルチサーバ設定** は、複数 MCP サーバを一元管理する標準フォーマットの存在を示す。本ボルト運用（[[04_カーパシーのObsidian活用術 30分で第二の脳]] の AI エージェント＋ツール連携）での実践的参照点
- **stdio・SSE・Streamable HTTP のトランスポート対応** は、[[09_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤]]（MCP インターフェース・3ツール群）と同様に「エージェントとツールを繋ぐ層」の標準化

## 関連

- MCP サーバー選定（Boris 8選） → [[02_Claude Code開発者ボリス推奨 MCPサーバー8選]]
- 契約・schema（node 契約） → [[03_Graph Engineering with Claude 14-Step roadmap]]・[[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]
- MCP インターフェース（エージェントとツールの接続層） → [[09_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤]]
