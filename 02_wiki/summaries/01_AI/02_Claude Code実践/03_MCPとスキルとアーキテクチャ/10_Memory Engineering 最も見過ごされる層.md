---
title: "10_Memory Engineering 最も見過ごされる層"
tags: [summary, ai, memory-engineering, context-engineering, agent-design, pkm]
source: https://x.com/beamnxw/status/2084951600716530032
author: beamnxw（@beamnxw）
published: 2026-08-04
created: 2026-08-05
---

# Memory Engineering — 最も見過ごされる層

> **Memory Engineering はエージェントシステムで最も見過ごされる部分。コンテキストウィンドウばかり凝視し、メモリ層を設計しない。明確な write policy なきシステムは全てを等しく信じ・期限切れにせず、S/N 比が崩壊する。4要素（write policy・ストレージ backend・階層 retrieval・能動的 maintenance）。「Context engineering は今見るもの・Memory engineering は来週知っているもの」を決める。**

出典: [[90_Memory Engineering 最も見過ごされる層（出典）]]（@beamnxw, 2026-08-04。本文はクリップ全文＋コメントを使用）

---

## 一行で

@beamnxw による Memory Engineering の重要性提起。コンテキストウィンドウ（今モデルが見るもの）ばかり設計し、メモリ層（システムが来週も知っているもの）を設計しないミスを指摘。健全なメモリシステムの4要素を提示。

## Context Engineering vs Memory Engineering（核心）

| | 決めること |
|---|---|
| **Context engineering** | モデルが**今**見るもの |
| **Memory engineering** | システムが**来週も**知っているもの |

> ほとんどのチームはコンテキストウィンドウに夢中になり、メモリ層を properly 設計しない。

## メモリ層がない場合の失敗

明確な write policy がないと:
- **全てを保管しすぎ**・**全てを等しく信じ**・**期限切れにしない**
- **S/N 比が崩壊**し・**検索品質が死ぬ**

## 健全なメモリシステムの4要素

1. **Write policy**（何を・いつ・どんな形式で・どんな confidence で書くか）
2. **Correct storage backends**（異なるメモリ型に応じたストレージ）
3. **Hierarchical retrieval**（working memory first → semantic search → trust/recency filters）
4. **Active maintenance**（TTL・confidence decay・deduplication・compression）

## コメントの実務知見

- **@jurlycat**: 「write policy はほとんどのメモリシステムが技術的負債を静かに蓄積する場所」
- **@gippp69**: 「write policy beats bigger windows（より大きなウィンドウより write policy）」
- **@sindikitil**: 「retrieval こそ誰も正しくできいない部分。write と store は簡単。正しいメモリを正しいステップで引くのが全て」

---

## 本ボルト内の位置付け

本ポストは Context Engineering と Memory Engineering の峻別という、本ボルト運用の中核に関わる知見。

- **「Context engineering は今見るもの・Memory engineering は来週知っているもの」** は [[06_Context Engineering Claude Codeの文脈設計]]（発話前の約7,850トークン設計・削除優先・段階的開示）の対極として Memory 層を定義。Context と Memory は表裏
- **Write policy（何を・いつ・どんな形式で・どんな confidence で）** は本ボルトの Ingest 操作（CLAUDE.md・「raw-sources は読むだけ・wiki は Claude が生成」）と同じ。本ボルトの write policy は「人間は raw へ書く・nightly agent は wiki へ書く」の層分離（[[18_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]] の 1層1writer）
- **Hierarchical retrieval（working memory → semantic search → trust/recency）** は [[08_LangGraph Academy エージェント構築のコース]]（短期記憶=checkpoint・長期記憶=store namespace）・[[04_オントロジーでClaude性能向上 知識グラフの実測]]（知識グラフで検索品質向上）と同系
- **Active maintenance（TTL・confidence decay・dedup・compression）** は本ボルトの Lint 操作（矛盾・孤立ページ・重複概念の検出・古くなった記述の洗い出し）・[[18_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]]（nightly compiler・compiled knowledge rots・日曜 health pass）と同じ「メモリの能動的保守」
- **「S/N 比崩壊・検索品質死」** は [[08_LangGraph Academy エージェント構築のコース]]（長時間会話のトークン膨張・filtering/trimming/summarization）・[[05_Claude Codeの6層アーキテクチャ ダムループ]]（95%到達で要約でなく構造化抽出・pruning beats summarizing）と同じ課題
- **Trust/recency filters** は ccc（[[02_ccc-forgejo-actions自動配備]]）の査による検証（合格/不合格二値＝trust）・監査記録（recency）に直結。[[03_ccc関連事例調査 ボルト内の同じアプローチ]] 参照

## 本ボルト運用への示唆

本ボルトは現在 Context Engineering（index.md・log.md・Wikilink で「今見るもの」を構造化）に重いが、Memory Engineering（「来週も知っているもの」をどう維持するか）は Lint 操作（週1・手動）に頼っている。本ポストの4要素は本ボルトの Lint を強化する指針:
1. **Write policy の明文化** — CLAUDE.md の Ingest 規則を write policy として再認識
2. **ストレージ backend の層分離** — raw/wiki/index/log の各層が既に backend 分離
3. **階層 retrieval** — index.md（working memory）→ Wikilink（1 hop）→ 全文検索 の階層を明示
4. **能動的 maintenance** — Lint を nightly compiler 化（[[18_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]]）する次段階

---

## 関連

- Context Engineering（対極・表裏） → [[06_Context Engineering Claude Codeの文脈設計]]
- 1層1writer・nightly compiler → [[18_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]]
- 短期/長期記憶・階層 retrieval → [[08_LangGraph Academy エージェント構築のコース]]
- 検索品質（知識グラフ） → [[04_オントロジーでClaude性能向上 知識グラフの実測]]
- pruning beats summarizing（S/N 比維持） → [[05_Claude Codeの6層アーキテクチャ ダムループ]]
- ccc の trust（査の検証）・recency（監査記録） → [[03_ccc関連事例調査 ボルト内の同じアプローチ]]
- GraphRAG（検索品質向上） → [[02_GraphRAG 知識グラフでRAGを置き換える]]
