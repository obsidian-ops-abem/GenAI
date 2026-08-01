---
title: "38_graphify コードベースを知識グラフ化（出典）"
tags: [raw-source]
source: https://github.com/Graphify-Labs/graphify
repo: https://github.com/Graphify-Labs/graphify
author: Graphify-Labs
published:
created: 2026-08-01
---

# 出典メタデータ

- リポジトリ: https://github.com/Graphify-Labs/graphify
- 公式/Enterprise: https://graphify.com/
- 開発元: Graphify-Labs
- 形態: GitHub README（OSS、Trendshift 掲載）
- パッケージ: PyPI `graphifyy`（double-y、CLIコマンドは `graphify`）
- タイトル: **graphify — Turn any codebase into a queryable knowledge graph**

要約は [[07_graphify コードベースを知識グラフ化]] を参照。

> [!info] 本文取得について
> 本記事はユーザーが Clippings フォルダへクリップした README 全文を提供したため、それに基づき転記・再構成。

---

# 本文（再構成）

## 一行で

AIコーディングアシスタントで `/graphify` と打つと、プロジェクト全体（コード・docs・PDF・画像・動画）を **クエリ可能な知識グラフ**にマッピングし、ファイルを grep する代わりに**グラフを問い合わせる**ようにする `/graphify` スキル。

## 核心特徴

- **コードは無料・完全ローカル** —— tree-sitter AST で決定的にパース。**LLM不使用、何もマシンを出ない**
- **全エッジに説明付き** —— 各接続は `EXTRACTED`（ソースに明示）または `INFERRED`（graphifyが解決）のタグ付き。直接読まれたものと推論されたものを区別できる
- **ベクトルインデックスではない** —— 埋め込みなし、ベクトルストアなし。**トラバースするリアルなグラフ**。質問する、2つのものの間のパスを追う、1つの概念を説明する

> docs/PDF/画像/動画は、アシスタントのモデル（または設定した API キー）でセマンティックパスを行う。

## 3つの出力ファイル

```
graphify-out/
├── graph.html       ブラウザで開く — クリック・フィルタ・検索
├── GRAPH_REPORT.md  ハイライト: 主要概念、意外な接続、推奨質問
└── graph.json       完全グラフ — ファイル再読込なしでいつでもクエリ
```

## クエリ例（FastAPI コードベースでの実出力）

```
$ graphify explain "APIRouter"
Node: APIRouter
  Source:    routing.py L2210
  Community: 2
  Degree:    47

Connections (47):
  --> RequestValidationError [uses] [INFERRED]
  --> Dependant [uses] [INFERRED]
  --> .get() [method] [EXTRACTED]
  <-- __init__.py [imports] [EXTRACTED]

$ graphify path "FastAPI" "ModelField"
Shortest path (3 hops):
  FastAPI --uses--> DefaultPlaceholder <--references-- get_request_handler() --references--> ModelField
```

- 各エッジは **信頼度タグ**（`EXTRACTED` / `INFERRED` / `AMBIGUOUS`）
- `graphify query "<question>"` は平易な質問に対しスコープ付きサブグラフを返す
- `graphify path A B` は2つのものがどう繋がるかをトレース

## 主な能力

| 能力 | 内容 |
|---|---|
| **God nodes** | 最も接続の多い概念（全てが何を通じて流れるか） |
| **Communities** | Leiden でサブシステムに分割（LLMフリーのラベル） |
| **Cross-file links** | `calls` / `imports` / `inherits` / `mixes_in` を tree-sitter AST で約40言語に解決 |
| **Query/path/explain** | `graph.json` に対し質問・パス・概念説明 |
| **Rationale + doc refs** | `# NOTE:` / `# WHY:` コメント、ADR/RFC 引用がコードに紐付く第一級ノードに |
| **Beyond code** | docs/PDF/画像/動画も同じグラフへ |
| **Local-first** | コードはローカル tree-sitter（LLM不使用）。docs/media のセマンティックパスのみバックエンド呼び出し（設定時のみ） |

## ベンチマーク

| Benchmark | Metric | graphify | Field |
|---|---|---|---|
| LOCOMO (n=300) | recall@10 | **0.497** | mem0 0.048, supermemory 0.149 |
| LOCOMO (n=300) | QA accuracy | 45.3% | supermemory 49.7%, mem0 27.3% |
| LongMemEval-S (n=50) | QA accuracy | **76%** | dense RAG と同率 |
| Graph build | LLM credits | **0** | 他システムは per-token |

同じハーネス・同じモデル・同じ予算で評価。judge は第2 judge で盲検妥当化（一致率 90.6%、Cohen's kappa 0.81）。

## 対応プラットフォーム（20+）

Claude Code / CodeBuddy / Codex / OpenCode / Kilo Code / GitHub Copilot CLI / VS Code Copilot Chat / Aider / OpenClaw / Factory Droid / Trae / Gemini CLI / Hermes / Kimi Code / Amp / Agent Skills (cross-framework) / Kiro IDE/CLI / Pi / Cursor / Devin CLI / Google Antigravity

## インストール（30秒）

```
uv tool install graphifyy      # または pipx install graphifyy
graphify install               # AIアシスタントへスキル登録
```

アシスタント内で: `/graphify .`

## 「常にグラフを使わせる」仕組み

プラットフォーム別コマンド（例: `graphify claude install`）が、コードベース質問でナレッジグラフを参照するようアシスタントへ指示:
- **Hook プラットフォーム**（Claude Code, Gemini CLI）: PreToolUse フックが検索系ツール呼び出しの前に自動発火し、グラフパスへナッジ
- **指示ファイル プラットフォーム**（Codex, OpenCode, Cursor）: `AGENTS.md` / `.cursor/rules/` 等の永続指示ファイルが同じ query-first ガイダンスを提供
- **Strict mode**（Claude Code）: `--strict` で最初の raw source read をブロックしグラフへリダイレクト（1セッション最大1回、以降は soft nudge に戻る）

## 重要な設計

- **コードはローカル AST**（tree-sitter、APIキー不要、オフライン動作）。`--code-only` で LLM 必要な docs/PDFs/images をスキップ可能
- **データ駐在**: `--backend ollama`（完全ローカル）または明示的 `--backend` フラグ。Kimi は中国の Moonshot AI サーバーへルーティング
- **テレメトリなし**、使用追跡なし
- **クエリログ**: `graphify query` / `path` / `explain` / MCP `query_graph` は `~/.cache/graphify-queries.log`（JSON Lines）へ記録。完全サブグラフ応答はデフォルト未保存。`GRAPHIFY_QUERY_LOG_DISABLE=1` で opt-out

## MCP サーバー機能

```
python -m graphify.serve graphify-out/graph.json                      # stdio（開発者ごとに1サーバー）
python -m graphify.serve graphify-out/graph.json --transport http --port 8080  # チーム共有HTTP
```

構造化アクセス: `query_graph` / `get_node` / `get_neighbors` / `shortest_path` / `list_prs` / `get_pr_impact` / `triage_prs`

## PR 連携

- `graphify prs` — PRダッシュボード: CI状態、レビュー状態、worktreeマッピング、グラフ影響
- `graphify prs 42` — PR #42 のディープダイブ + グラフ影響
- `graphify prs --triage` — AI がレビューキューをランキング
- `graphify prs --conflicts` — グラフコミュニティを共有する PR（マージ順序リスク）

## Enterprise

[graphify Enterprise](https://graphify.com/) は always-on レイヤー。会議・ファイル・docs・コード全体へ同じグラフ手法を適用し、バックグラウンドで継続更新。数百の会話・ドキュメントにまたがり決して完全再構築できない仕事を持つ人/チーム向け。

---

## 位置付け

GraphRAG [[02_GraphRAG 知識グラフでRAGを置き換える]] の「コードベース特化・ローカル AST・ベクトル不使用」実装。本ボルトの運用（このVaultの `[[Wikilink]]` による知識ネットワーク）と同じ「検索でなくトラバース」思想だが、コード/ドキュメントを対象とする。`EXTRACTED`/`INFERRED` の信頼度タグは [[01_エージェントファクトリの作り方 ビルダーズガイド]] の「チェックできるものを強制、判断しかできないものは閾値」思想と共鳴。

---

## 関連（ボルト内）

- GraphRAG（知識グラフでRAG置換） → [[02_GraphRAG 知識グラフでRAGを置き換える]]
- 視覚ベースRAG（別アプローチ） → [[04_PixelRAG スクショで検索するRAG]]
- 信頼度タグ・検証 → [[01_エージェントファクトリの作り方 ビルダーズガイド]]
- Context節約（serena の16kトークン問題と関連） → [[37_Graph of Loops（仮）]] L3・[[06_Context Engineering Claude Codeの文脈設計]]
- エージェントのContextからコードベース理解 → [[05_デジタル庁 ChatGPTを業務に組み込むためのハンズオン]]
