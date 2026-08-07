---
title: "05_DynamoDB Vector Search リアルタイムベクトル検索GA"
tags: [summary, aws, dynamodb, vector-search, rag, agent-memory, platform]
source: https://aws.amazon.com/jp/about-aws/whats-new/2026/08/amazon-dynamodb-vector-search/
author: Amazon Web Services
published: 2026-08-06
created: 2026-08-06
---

# DynamoDB Vector Search — リアルタイムベクトル検索 GA

> **DynamoDB がネイティブベクトル検索を GA 化。1桁ミリ秒レイテンシ・99%+ recall・任意スケール（兆ベクトルまで）。ベクトル埋め込みを他の属性と並列保存・パーティションキーでスケール・属性フィルタで結果を絞る。serverless（インフラ/ダウンタイム/メンテナンスゼロ・使った分だけ支払い）。AI エージェントのメモリに意味検索を追加し grounding を強化。**

出典: [[94_DynamoDB Vector Search リアルタイムベクトル検索GA（出典）]]（AWS What's New, 2026-08-06。本文はクリップ全文を使用）

---

## 一行で

AWS が DynamoDB のベクトル検索を GA 化。DynamoDB 上でベクトル埋め込みを保存し、1桁ミリ秒・99%+ recall で ANN（近似最近傍）検索が可能。agent メモリの意味検索・RAG・類似検索等を serverless で。

## 核心

| 項目 | 内容 |
|---|---|
| **レイテンシ** | 1桁ミリ秒（single-digit ms） |
| **recall** | 99%+ |
| **スケール** | 任意（兆ベクトルまで） |
| **保存** | ベクトル埋め込みを他の属性と並列保存 |
| **モデル** | 任意（Amazon Bedrock モデル含む） |
| **スケーリング** | vector index の partition key を選択 |
| **フィルタ** | 属性で結果を絞る（scope results） |
| **serverless** | インフラ管理ゼロ・ダウンタイムゼロ・メンテナンスウィンドウゼロ・使った分だけ支払い |

## 従来のトレードオフと解決

ベクトルデータセットが数十億〜兆規模に成長すると、従来は**検索速度・スケール・精度のトレードオフ**が生じる（ベクトル数増でレイテンシ上昇・recall/throughput 低下を受け入れる必要）。DynamoDB はこれを**1桁 ms × 99%+ recall × 兆スケール**で解消。

## ユースケース

- **AI エージェントのメモリに意味検索を追加**（agentic grounding）— DynamoDB は既に agent memory ストアとして使用可能だが、vector search でそのメモリ上の意味検索が可能に
- 製品類似検索・パーソナライズド広告・RAG・レコメンドシステム

---

## 本ボルト内の位置付け

- **agent メモリ + ベクトル検索（agentic grounding）** は [[10_Memory Engineering 最も見過ごされる層]]（hierarchical retrieval: working memory → semantic search → trust/recency filters）・[[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]（file systems as memory・production 4原則の portability）の**バックエンド実装**。DynamoDB は semantic retrieval 層を serverless で提供
- **RAG/検索系** は [[02_PixelRAG スクショで検索するRAG]]（視覚ベース RAG）・[[03_graphify コードベースを知識グラフ化]]（知識グラフ・AST）・[[04_オントロジーでClaude性能向上 知識グラフの実測]]（オントロジーでコスト26%減）・[[02_GraphRAG 知識グラフでRAGを置き換える]] と同系。DynamoDB vector search はこれらのストレージバックエンド候補
- **LangGraph の long-term store** ([[08_LangGraph Academy エージェント構築のコース]]・namespace でメモリ型分離) の production バックエンドとしても適する（PostgreSQL/SQLite に加えて DynamoDB）
- **1桁 ms × 99%+ recall × 兆スケール** は [[01_知識グラフメモリをOpus5で安く運用する]]（コスト最適化）と同じ「スケール時の性能維持」課題への回答
- **serverless（インフラゼロ）** は [[01_Vibe Kanban コーディングエージェント用Kanbanワークスペース]]・[[09_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤]] と同じ「管理不要インフラ」系譜

---

## 関連

- Memory Engineering（semantic retrieval 層） → [[10_Memory Engineering 最も見過ごされる層]]
- file systems as memory（portability） → [[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]
- GraphRAG（知識グラフで RAG 置換） → [[02_GraphRAG 知識グラフでRAGを置き換える]]
- PixelRAG（視覚ベース RAG） → [[02_PixelRAG スクショで検索するRAG]]
- graphify（コードベース知識グラフ化） → [[03_graphify コードベースを知識グラフ化]]
- オントロジーでコスト削減 → [[04_オントロジーでClaude性能向上 知識グラフの実測]]
- 知識グラフメモリのコスト最適化 → [[01_知識グラフメモリをOpus5で安く運用する]]
- LangGraph long-term store → [[08_LangGraph Academy エージェント構築のコース]]
