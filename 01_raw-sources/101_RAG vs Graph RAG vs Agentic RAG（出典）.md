---
title: "101_RAG vs Graph RAG vs Agentic RAG（出典）"
tags: [raw-source]
source: https://x.com/DailyDoseOfDS_/status/2085659743587365161
author: Daily Dose of Data Science (@DailyDoseOfDS_)
published: 2026-08-07
created: 2026-08-02
---

# 出典メタデータ

- ポストURL: https://x.com/DailyDoseOfDS_/status/2085659743587365161
- 著者: Daily Dose of Data Science（@DailyDoseOfDS_）
- 公開: 2026年8月7日
- 形態: X 長文ポスト
- タイトル: **RAG vs Graph RAG vs Agentic RAG**（推定）

## 概要

3つのRAGアーキテクチャの違いを比較解説。これらは「洗練度の段階」ではなく、**異なるクエリタイプを解決する**並列アプローチ。

### 3アーキテクチャ

| クエリタイプ | アーキテクチャ | 特徴 |
|---|---|---|
| 単一ホップの事実検索 | **Standard RAG** | 埋め込み類似度でチャンク検索 |
| マルチホップの関係クエリ | **Graph RAG** | 知識グラフ層を追加、エンティティ・関係を抽出しグラフトラバーサル |
| 動的マルチソース・ツール使用 | **Agentic RAG** | LLMエージェントがクエリ時にツール・ソース・順序を決定 |

### Graph RAG が効く例

「決済サービスは金曜のメンテナンスの影響を受けるか？」
- 事実1: checkout service は payments API を使用
- 事実2: payments API は cluster-3 で稼働
- 事実3: cluster-3 は金曜メンテ予定

Vector search は事実1・3を検索できるが、事実2（「checkout」も「maintenance」も含まない）を埋め込み空間で見逃す。知識グラフはこれをリンクされたエンティティとして接続し、1クエリで全パスを発見。

### コメント欄の知見

- **評価指標もアーキテクチャに合わせて変えるべき**: retrieval recall（標準）／ path completeness（Graph）／ tool selection + recovery（Agentic）。単一の正解精度メトリックは障害を隠す
- **Graph RAG の実コストは index time**: 大規模コーパスでのエンティティ抽出が重い
- **Hypotree（MCP server）**: 記憶をDAGとして構築、死んだ枝を自動刈り込み、消去法で結論を導出

---

## 関連（ボルト内）

- Graph Engineering replaced RAG → [[06_Graph Engineering replaced RAG（出典）]]
- DynamoDB Vector Search → [[94_DynamoDB Vector Search リアルタイムベクトル検索GA（出典）]]
- PixelRAG → [[25_PixelRAG スクショで検索するRAG（出典）]]
