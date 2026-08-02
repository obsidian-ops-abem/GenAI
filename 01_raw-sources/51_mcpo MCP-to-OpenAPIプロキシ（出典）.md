---
title: "51_mcpo MCP-to-OpenAPIプロキシ（出典）"
tags: [raw-source]
source: https://x.com/DanKornas/status/2083309467798544836
repo: https://github.com/open-webui/mcpo
author: DanKornas (@DanKornas)
published: 2026-08-01
created: 2026-08-01
---

# 出典メタデータ

- ポストURL: https://x.com/DanKornas/status/2083309467798544836
- GitHub: https://github.com/open-webui/mcpo
- 紹介者: DanKornas（@DanKornas）— AI/ML/agents ニュースレター
- 著者（リポジトリ）: open-webui
- 公開: 2026年8月1日
- 形態: X ポスト（OSS 紹介）＋ GitHub README
- ライセンス: MIT

要約は [[03_mcpo MCP-to-OpenAPIプロキシ]] を参照。

> [!info] 本文取得について
> 本記事はユーザーが Clippings フォルダへクリップした全文（日本語ポスト）を提供。※ポストは紹介のみ、機能詳細は言及から再構成。

---

# 本文（再構成）

## MCP ツールを OpenAPI アプリへ繋ぐ

多くの MCP ツールは、**HTTP と OpenAPI を中心に構築されたアプリ**に接続するのが不格好。

**mcpo**（open-webui/mcpo）は、**MCP ツールを OpenAPI 互換のエージェントやアプリ**で利用できるようにするための **MCP-to-OpenAPI プロキシ**。MCP サーバのコマンド/エンドポイントを取り、**OpenAPI スキーマとインタラクティブドキュメントを生成**して HTTP インターフェースの背後に置く。

### 主な特徴（5点）

| 特徴 | 内容 |
|---|---|
| **OpenAPI プロキシ** | MCP ツールを標準 HTTP エンドポイントで公開 |
| **インタラクティブドキュメント** | 各ツールのドキュメント UI を自動生成 |
| **トランスポートサポート** | **stdio・SSE・Streamable HTTP** の MCP サーバに対応 |
| **マルチサーバ設定** | Claude Desktop 形式の設定ファイルから複数 MCP ツールを提供 |
| **OAuth 2.1** | 保護された Streamable HTTP MCP サーバの認証をサポート |

オープンソース（MIT ライセンス）。
