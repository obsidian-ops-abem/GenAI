---
title: "10_マルチエージェントでナレッジグラフ構築 Neo4j×Google ADK"
tags: [summary, ai, knowledge-graph, neo4j, google-adk, multi-agent, rag, graphrag, cypher]
source: DeepLearning.AI コース「Building a Multi-Agent System to Construct a Knowledge Graph」
author: Andrew Ng × Stennett Elliott（Neo4j）※
published: 2026-08-04
created: 2026-08-04
---

# マルチエージェントでナレッジグラフを構築する — Neo4j × Google ADK

> **構造化データ（CSV）と非構造化データ（Markdown）をナレッジグラフに変換するマルチエージェントシステムの構築コース。Google ADK でルートエージェント配下に構造化/非構造化ワークフローを置き、user intent → file suggestion → schema proposal（critic パターン）→ graph construction plan を生成。3層グラフ（domain/lexical/subject）で構造化と非構造化を接続。Cypher のパターンマッチングと natural language の相性の良さを強調。**

出典: [[79_マルチエージェントでナレッジグラフ構築 Neo4j×Google ADKコース（出典）]]（Andrew Ng × Stennett Elliott ※, 2026-08-04。本文はユーザー提供の文字起こし全文を使用。※講師名は ASR「on this colleague」からの推定・NewFJ=Neo4j・Light LLM=LiteLLM・four is=4o 等の誤変換あり）

---

## 一行で

DeepLearning.AI の短期コース。Neo4j の Stennett Elliott が講師を務め、Google ADK（Agent Development Kit）でマルチエージェントシステムを組み、CSV（構造化）と Markdown（非構造化）を統合するナレッジグラフを構築する。推奨クエリやルート原因分析など、リレーショナルDBの JOIN では複雑になる問いをグラフのパターンマッチ（Cypher）で簡潔に解く前提知識から入り、実装までを網羅。[[02_GraphRAG 知識グラフでRAGを置き換える]] の実装系コース版。

## なぜナレッジグラフか（核心動機）

リレーショナルDBでの「ABKが買った製品を買った他の人々が買った、ABKがまだ買っていない製品」（レコメンド）は JOIN が3段で複雑。グラフなら**ノード（人/製品）+ リレーション（purchased）+ パターンマッチ**で、存在すべきパターンと存在すべきでないパターン（ABK→couch のリレーションが無い）を一度に表現できる。

- ノードとリレーションは共に**第一級のデータレコード**（意味を持つ・プロパティ付き）
- リレーションは常に方向を持ち、単一タイプを持つ
- クエリ言語 **Cypher** は SQL にパターンマッチを加えたもので、**natural language と相性が良い**（生成モデルにマップしやすい）
- ベクトル類似度とパターンマッチを組み合わせることで強力な検索が可能

## 3層グラフモデル（核心アーキテクチャ）

| 層 | データ | 役割 |
|---|---|---|
| **domain graph** | CSV（構造化データ） | パターンマッチでクエリ可能な構造化データのグラフ |
| **lexical graph** | Markdown（非構造化データ） | 元のテキストとその構造。チャンク化・ベクトル埋め込み |
| **subject graph**（中間） | チャンクから抽出したエンティティ | subject-predicate-object（例: ABK -loves-> table）。**構造化データの product ノードと接続**する橋渡し |

ルート原因分析（家具メーカーのクレーム分析等）で「どの製品に問題が多いか→どの部品が原因か→設計問題か製造問題か」を追うのに適する。

## マルチエージェントシステムの構成

ルートエージェント（会話型・仕事はしない・ユーザーをワークフローに導く）配下に3ワークフロー：

### 構造化データワークフロー（サブエージェント群）
1. **user intent agent** — ユーザーのゴールと作りたいグラフの型を抽出。出力はユーザーの方向性
2. **file suggestion agent** — ゴールに基づき利用可能ファイルから有用なものを提案。出力は承認済みファイルリスト
3. **schema proposal agent**（critic パターン・2エージェント対） — 一方がスキーマを提案し他方が批判。自己批判ループで**graph construction plan**（グラフそのものでなく構築方法の記述）を生成

### 非構造化データワークフロー
- 前半2ステップ（user intent / file suggestion）は構造化と共通
- 第3ステップは異なる：テキストからエンティティ（人/場所/物）と事実（facts）を抽出する2専門エージェント。**抽出そのものでなく何が抽出可能かを記述**した **knowledge extraction plan** を生成

### 統合
- graph construction plan ＋ knowledge extraction plan が全ルール
- 最後の単一ツールが両 plan を取り、実際の抽出と構築を実行（構築ルールでメイングラフ生成→Markdown をチャンク化・ベクトル埋め込み→エンティティ/事実抽出→構造化データへ接続）

## Google ADK の基本（Lesson 3）

### エージェントの定義要素
- `name`（バージョン管理・デバッグに重要）
- `model`（LiteLLM 経由で OpenAI GPT-4o）
- `description`（**他エージェントが「いつこのエージェントを使うか」を知る委譲の鍵**）
- `instructions`（エージェント自身の自己理解・役割・ツール使用タイミング）
- `tools`（関数名の配列）

**ベストプラクティス**：マルチエージェントを組むと、設定後は**instructions と description の最適化に最も時間をかける**（classic prompt engineering に戻る）。

### ツールの docstring が重要
ツール関数の docstring は LLM に渡る「このツールが何をするか」の説明。引数・結果・想定エラーを記述。

### 実行環境（runner）
- `session`（実行の単位・本番では複数）
- `InMemorySessionService`（コンテキストと state を提供）
- `runner`（イベントループ管理・LLM 呼び出し・エージェント間調整）
- コースでは `AgentCaller` ヘルパークラスでラップ

### tool context とメモリ
- `ToolContext` はセッション状態（state 辞書）へのアクセスを提供。Google ADK はツールの最後の引数が `tool_context` だと**自動注入**する
- state はエージェント/ツール間で共有（`say_hello` で username を保存 → `say_goodbye` が username を読む）
- `output_key` でエージェントの最終応答を state に保存可能
- 本番では DB 永続化推奨、コースでは in-memory

### エージェント委譲 vs ツール呼び出し
委譲はツール呼び出しに似るが、**会話履歴全体と制御を相手エージェントに渡す**点が違う。ルートエージェントは自身で応答せず、サブエージェントに制御を移す（transfer to agent）。

## Neo4j × Google ADK の統合（neo4j_for_adk ラッパー）
- Neo4j ドライバを初期化し、クエリ送信と結果フォーマットを ADK 向けに整形
- 結果は `{status: success/error, ...}` 辞書形式（ADK の期待）
- Neo4j の結果をシリアライズ可能にする `to_python` ヘルパー
- **クエリパラメータ（`$param`）の使用を強調**：SQL/Cypher インジェクション対策（文字列結合でなく値として渡す）

## 関連

- [[02_GraphRAG 知識グラフでRAGを置き換える]] — GraphRAG の概念。本コースはその実装系（Neo4j で構築する方法）
- [[08_LangGraph Academy エージェント構築のコース]] — 別フレームワーク（LangGraph）のエージェント構築コース。本コースは Google ADK 版。両者で state/memory/checkpoint の概念が共通
- [[03_LangChain エージェント・エンジニアリング・プラットフォーム]] — LangChain/Core/Deep Agents。本コースは Google ADK という別スタック
- [[04_Graph Architectへの20ステップ5フェーズ]] — グラフ設計の学習ロードマップ。本コースはその「データ由来のグラフ」版（エージェント設計でなくデータからのグラフ構築）
- [[07_Boris対談 Anthropic内部Claude運用とエージェント設計]] — evals と vibes の使い分け。本コースの critic パターン（schema proposal）は小規模な内部 evals に相当
- [[05_LangGraphワークショップ 信頼性のあるエージェントの構築とテスト]] — 「The model is not the moat」。本コースのスキーマ自動生成もモデルに依存しない確定的な構築ルールへの抽象化

## 所感

「description と instructions の最適化に最も時間を使う」は、マルチエージェントの設定後の作業の実態をよく表す。[[06_5層モデル各層の作業単位 プロンプトからグラフへ]] の「プロンプトは3層上位のエラーの責任を負わされる」の警告通り、instructions の質がシステム全体に影響する。3層グラフ（domain/lexical/subject）で構造化と非構造化を繋ぐ設計は、GraphRAG 実装の汎用パターンとして参考になる。critic パターン（schema proposal agent 対）は、[[04_自己レビューエージェントのGraph設計 Anthropicメソッド]] の「わざと壊す→ルールブック」の小規模版。ASR 品質に課題（講師名推定含む）だが、コース構造とコード例は明確。
