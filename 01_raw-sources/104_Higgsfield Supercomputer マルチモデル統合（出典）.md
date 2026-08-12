---
title: "104_Higgsfield Supercomputer マルチモデル統合（出典）"
tags: [raw-source]
source: https://x.com/AnatoliKopadze/status/2083153427777933733
author: Anatoli Kopadze (@AnatoliKopadze)
published: 2026-07-31
created: 2026-08-02
---

# 出典メタデータ

- ポストURL: https://x.com/AnatoliKopadze/status/2083153427777933733
- 著者: Anatoli Kopadze（@AnatoliKopadze）
- 公開: 2026年7月31日
- 形態: X 長文ポスト（ノート）
- タイトル: **Stop Switching Between Claude, GPT and Grok. Run Them All at Once.**

## 概要

Higgsfield Supercomputer は、Claude・GPT・Gemini・Grok を1箇所で実行し、各タスクに最適なモデルを自動ルーティングするプラットフォーム。

### 通常のサブスクとの違い

- 通常: 1つの優れたモデルをチャットボックスで使用。ツール切替・結果コピペ・コンテキスト維持はすべて手動
- Supercomputer: 複数モデルを1ウィンドウで実行。コンテキストとスタイルをセッション間で記憶。ツール連携。スケジュール実行

### コスト構造

- **Planning・research・drafts は Free mode で無料**
- 最終出力のレンダリング時のみ課金
- 10方向試して9つ捨て、効いた1つにだけ課金 → Explore first, commit later

### 3つの開始方法

1. **ブラウザ**: Higgsfield サイトでチャット開始（セットアップ不要）
2. **Claude 内**: Settings → Connectors → カスタムコネクタ追加（URL: `https://mcp.higgsfield.ai/mcp`）。MCP経由で Claude からタスク委譲
3. **Claude Code**: `claude mcp add higgsfield https://mcp.higgsfield.ai/mcp`
4. **CLI**: `npm install -g @higgsfield/cli` + `higgsfield auth login` + `npx skills add higgsfield-ai/skills`

### ノウハウ

- **References**: 言葉で全部説明せず、好きなサイト・商品・動画のリンクを入れて「これっぽく作って、Xだけ変えて」と指示
- **Reusable blocks**: 変わらない部分（スタイル・コンテキスト・ルール）を固定ブロックにし、タスク固有部分だけ書き換え。定数は1回設定、変数だけ変える

### 18の具体例（カテゴリ）

Build and development（アプリ構築・ランディングページ＋広告）、他カテゴリあり。

---

## 関連（ボルト内）

- mcpo（MCP-to-OpenAPI プロキシ） → [[51_mcpo MCP-to-OpenAPIプロキシ（出典）]]
- MCP サーバー活用 → [[04_Claude Code開発者ボリス推奨 MCPサーバー8選（出典）]]
