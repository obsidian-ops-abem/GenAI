---
title: "03_graphify コードベースを知識グラフ化"
tags: [summary, tool, knowledge-graph, rag, claude-code, ast]
source: https://github.com/Graphify-Labs/graphify
repo: https://github.com/Graphify-Labs/graphify
author: Graphify-Labs
created: 2026-08-01
---

# graphify —— コードベースをクエリ可能な知識グラフへ

> **「grep する代わりにグラフに問い合わせる」** — コード/docs/PDF/画像/動画を知識グラフ化する `/graphify` スキル。コードは tree-sitter AST で**完全ローカル・LLM不使用・ベクトル不使用**。

出典: [[38_graphify コードベースを知識グラフ化（出典）]]（Graphify-Labs。本文はユーザー提供クリップから再構成）

---

## 一行で

AI コーディングアシスタントで `/graphify` と打つと、プロジェクト全体を **クエリ可能な知識グラフ**にマッピングし、ファイルを grep する代わりに**グラフをトラバース**して問い合わせる OSS（Claude Code/Cursor/Codex/Gemini CLI 等 20+ 対応）。

## 3つの核心特徴

1. **コードは無料・完全ローカル** —— tree-sitter AST で決定的パース。**LLM不使用、何もマシンを出ない**（docs/PDF/画像/動画のみモデル使用）
2. **全エッジに説明付き** —— `EXTRACTED`（ソース明示）/ `INFERRED`（graphify解決）/ `AMBIGUOUS` の信頼度タグ。直接読んだものと推論を区別
3. **ベクトルインデックスではない** —— 埋め込みもベクトルストアもなし。**トラバースするリアルグラフ**

## 3つの出力ファイル

```
graphify-out/
├── graph.html       ブラウザでクリック・フィルタ・検索
├── GRAPH_REPORT.md  ハイライト: 主要概念・意外な接続・推奨質問
└── graph.json       完全グラフ — ファイル再読込なしでクエリ
```

## クエリ例（FastAPI 実出力）

```
$ graphify explain "APIRouter"
Node: APIRouter
  Source: routing.py L2210 | Community: 2 | Degree: 47
  --> RequestValidationError [uses] [INFERRED]
  --> .get() [method] [EXTRACTED]
  <-- __init__.py [imports] [EXTRACTED]

$ graphify path "FastAPI" "ModelField"
Shortest path (3 hops):
  FastAPI --uses--> DefaultPlaceholder <--references-- get_request_handler() --references--> ModelField
```

## 主な能力

| 能力 | 内容 |
|---|---|
| **God nodes** | 最も接続の多い概念 |
| **Communities** | Leiden でサブシステム分割（LLMフリーラベル） |
| **Cross-file links** | `calls`/`imports`/`inherits`/`mixes_in` を tree-sitter で約40言語に解決 |
| **Rationale + doc refs** | `# NOTE:`/`# WHY:` コメント・ADR/RFC がコードに紐付く第一級ノード |
| **Beyond code** | docs/PDF/画像/動画も同じグラフへ |

## ベンチマーク（他システムと同ハーネス・同モデル・同予算）

| Benchmark | graphify | 比較 |
|---|---|---|
| LOCOMO recall@10 | **0.497** | mem0 0.048, supermemory 0.149 |
| LongMemEval-S QA | **76%** | dense RAG と同率 |
| Graph build LLM credits | **0** | 他は per-token |

## インストール（30秒）

```
uv tool install graphifyy      # パッケージは graphifyy（double-y）
graphify install               # スキル登録
/graphify .                    # アシスタント内で実行
```

## 「常にグラフを使わせる」仕組み

プラットフォーム別（例: `graphify claude install`）:
- **Hook プラットフォーム**（Claude Code/Gemini CLI）: PreToolUse フックが検索系ツール呼び出し前に自動発火
- **指示ファイル プラットフォーム**（Codex/OpenCode/Cursor）: `AGENTS.md`/`.cursor/rules/` が query-first を指示
- **Strict mode**: 最初の raw read をブロックしグラフへリダイレクト（1セッション最大1回）

## 重要な設計

- **コードはローカル AST**（`--code-only` で LLM 不要な docs/PDFs をスキップ可能、APIキー不要・オフライン）
- **データ駐在**: `--backend ollama`（完全ローカル）または明示フラグ。Kimi は中国サーバーへルーティング
- **テレメトリなし**。クエリログは opt-in（デフォルトで subgraph 応答未保存）
- **MCP サーバー**: `python -m graphify.serve`（stdio=開発者ごと / http=チーム共有）
- **PR 連携**: `graphify prs`（CI状態・レビュー・worktree・グラフ影響）、`--triage`（AIランキング）、`--conflicts`（コミュニティ共有=マージ順序リスク）

## Enterprise

[graphify.com](https://graphify.com/) は always-on レイヤー。会議・ファイル・docs・コード全体へ同じグラフ手法を適用し、バックグラウンド継続更新。数百の会話・ドキュメントを再構築できない仕事向け。

---

## 本ボルト内の位置付け

**GraphRAG [[02_GraphRAG 知識グラフでRAGを置き換える]] の「コードベース特化・ローカルAST・ベクトル不使用」実装**:

| 視点 | 対応 |
|---|---|
| RAG の系譜 | [[02_GraphRAG 知識グラフでRAGを置き換える]]（知識グラフでRAG置換）の具現化、[[04_PixelRAG...]]（視覚ベース）とは別軸 |
| 信頼度タグ | `EXTRACTED`/`INFERRED` は [[01_エージェントファクトリの作り方 ビルダーズガイド]]「チェックできるものを強制、判断は閾値」と共鳴 |
| Context節約 | serena の16kトークン問題（[[03_Graph of Loops Claude Code完全システム10リポジトリ]] L3）と関連 —— グラフクエリで読み込み削減 |
| 本ボルト自身 | このVaultの `[[Wikilink]]` ネットワークと同じ「検索でなくトラバース」思想だが、コード/ドキュメントが対象 |

`/graphify` は**このVaultの知識グラフアプローチをコードベースへ適用するツール**と言える。Obsidian Vault と graphify-out/ は鏡写しの関係。

## 関連

- GraphRAG（知識グラフでRAG置換） → [[02_GraphRAG 知識グラフでRAGを置き換える]]
- 視覚ベースRAG（別アプローチ） → [[02_PixelRAG スクショで検索するRAG]]
- 信頼度タグ・検証 → [[01_エージェントファクトリの作り方 ビルダーズガイド]]
- Context節約・serena の16k問題 → [[03_Graph of Loops Claude Code完全システム10リポジトリ]]・[[06_Context Engineering Claude Codeの文脈設計]]
- 第二の脳（本Vaultと同思想） → [[01_Claude×Obsidianで第二の脳を作る]]・[[02_Claude Code×Obsidianで第二の脳を作る 2000時間の設計ルール]]
