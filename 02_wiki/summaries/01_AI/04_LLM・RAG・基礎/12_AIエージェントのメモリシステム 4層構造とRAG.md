---
title: "12_AIエージェントのメモリシステム 4層構造とRAG"
tags: [summary, ai, memory, agent, rag, context, working-memory, semantic, episodic, procedural]
source: YouTube 動画（AI エージェントのメモリシステム解説）
author: 講師名不詳（YouTube 解説者）
published: 2026-08-04
created: 2026-08-04
---

# AI エージェントのメモリシステム — 4 層構造と RAG

> **ChatGPT/Claude の「記憶」の正体を解説。working memory（context RAM）に user prompt・chat history・system prompt を集約し、3柱の長期記憶（procedural=振る舞い/skills・semantic=耐久事実/RAG・episodic=時系列イベント）で補強。consolidation gate が一定チャット数後に summarizer agent を起動し、episodic から semantic へ統合（still into facts）。エフェメラルなセッションをデータベース爆炸させずに core memory に蒸留する設計。**

出典: [[82_AIエージェントのメモリシステム 4層構造とRAG（出典）]]（YouTube 動画, 2026-08-04。本文はユーザー提供の文字起こし全文を使用）

---

## 一行で

ChatGPT/Claude の記憶機能を「どう実装するか」を4層構造で解説する入門動画。短時間だが、エージェントメモリ設計の基本要素（working/semantic/episodic/procedural・RAG・consolidation gate）を一通り網羅。実務で ChatGPT の Memories を使ったことがある人には「あの機能の裏側」が分かる。

## アーキテクチャ全体

```
[User Prompt]
     ↓
[Working Memory / Context RAM] ← System Prompt + Current Chat History
     ↓                          ↑        ↑        ↑
[QA Agent (LLM)]          procedural  semantic  episodic
     ↓                     (skills)   (RAG)    (時系列)
[Reply]                        ↓        ↓        ↓
                           ベストプラクティス・耐久事実・過去ログ
```

エージェントセッション自体は **ephemeral（一時的）**。LLM 呼び出しだけで DB はない。永続化には明示的な保存が必要。

## 4層の記憶（核心）

### 1. Working Memory（コンテキスト RAM）
LLM がその瞬間に見ているコンテキスト。user prompt + system prompt（役割・例: 「Elon Musk として答えよ」）+ current chat history を集約。QA Agent はこれを処理して返答。

### 2. Procedural Memory（手続き的記憶）
エージェントの**振る舞いのルール**。skills として実装。
- 例:「顧客が怒っていたら丁寧に・必ず謝る」
- few-shot やベストプラクティスの保存場所

### 3. Semantic Memory（意味的記憶）
**耐久的な事実**。会社情報・製品カタログ・顧客プロファイル。
- 基盤モデルはあなたを知らない（有名人でなければ）ので DB に保存が必要
- **RAG（Retrieval Augmented Generation）** で top-k 検索して関連情報だけ取り出す
- 10年分の文書を全て LLM の1Mトークン窓に詰め込むと高価・遅・不正確 → smart な fetch が必要

### 4. Episodic Memory（エピソード的記憶）
**時系列のイベントログ**。過去の会話・購買履歴・クレーム記録など。
- vector store に埋め込まれ、RAG で検索
- semantic との違い：semantic は「不変の事実」、episodic は「時間付きの出来事」
- エージェントセッション終了後にここへ保存（箭印で reply → DB）

## consolidation gate（統合の門・核心メカニズム）

**問題**：すべての新活動を semantic memory に要約すると、情報を2回保存しているのと同じ（episodic にもあるのに semantic にもある）。何のため？

**解**：**gate**（門）を置き、一定数のチャット（20会話・100活動など）が溜まった後でのみ統合を実行。

```
[Nチャット後] → [Summarizer Agent] → semantic memory へ要約統合（"still into facts"）
```

これにより：
- ツール呼び出し回数削減（毎回巨大DBを検索しない）
- 応答高速化
- core memory（semantic）に重要事実だけ蒸留

→ ChatGPT の Memories が「短く・常に更新される」のはこの仕組み。ピボットの会話をすると memory が自動更新される。

## なぜ RAG なのか（強調ポイント）

- 10年分の会社データ（画像・テキスト・文書）を全て LLM のコンテキスト窓（約1Mトークン）に入れると：
  1. 高価
  2. 遅い
  3. 不正確（loss-in-the-middle）
- vector store がテキストを数字の配列に変換し、similarity search で top-k（K=5等）の関連情報だけを取り出す

## 関連

- [[02_GraphRAG 知識グラフでRAGを置き換える]] / [[10_マルチエージェントでナレッジグラフ構築 Neo4j×Google ADK]] — RAG の発展形。本動画はその前提となる基本 RAG とメモリ階層
- [[01_知識グラフメモリをOpus5で安く運用する]] / [[05_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]] — 長期記憶の実装。本動画の4層構造を大規模にしたもの
- [[06_5層モデル各層の作業単位 プロンプトからグラフへ]] — context engineering 層「メモリ・何を削除するかを知る」の具体化。working memory がその層に対応
- [[09_ジェネレータ×エバリュエータハーネスで長時間走るエージェント]] — 「compaction ≠ 一貫性・drift」の問題。本動画の consolidation gate はその緩和策だが、lossy summary の限界も示唆
- [[07_Boris対談 Anthropic内部Claude運用とエージェント設計]] — Anthropic が Claude を全プロセスの中心に。memory system はその技術的土台
- [[01_Claude×Obsidianで第二の脳を作る]] — 本ボルトの運用ルール。raw-sources/02_wiki の3層が procedural(=CLAUDE.md)/semantic(=wiki要約)/episodic(=log.md時系列) に似る

## 所感

4層構造（working/procedural/semantic/episodic）は認知科学の記憶分類（Tulving 等）の AI への援用で、実装パターンの共通語彙として有用。consolidation gate は実務上の重要ポイント：毎会話ごとに要約するとコストと重複の問題が起きるため「Nチャット後にまとめて統合」が現実的解。本ボルトの運用ルール自体がこの4層に似ていることに気づく：CLAUDE.md=procedural・02_wiki/summaries=semantic・log.md=episodic・現在のコンテキスト=working。短い入門動画だが、エージェント設計の「記憶」を体系立てて理解するのに適した構成。
