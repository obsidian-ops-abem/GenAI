---
title: "13_RAGの基礎 知識カットオフからneedle in a haystackまで"
tags: [summary, ai, rag, llm, context, retrieval, knowledge-cutoff, grpo]
source: 学術講義（LLMコース・RAG/tool calling/agents 回）
author: 講師名不詳（大学教授・前回は reasoning model/GRPO を担当）
published: 2026-08-04
created: 2026-08-04
---

# RAG の基礎 — 知識カットオフから needle in a haystack まで

> **vanilla LLM の2つの弱点（古い知識・行動できない）のうち、前者への対応として RAG を導入。前回の GRPO/length bias の recap（double・GRU Don Rights）の後、ナイーブな「全情報をプロンプトに詰め込む」アプローチが3つの理由で破綻することを示し、retrieve/augment/generate の3ステップへ。needle in a haystack テストで「無関係情報の増加が性能劣化させる」ことを可視化。**

出典: [[83_RAGとtool callingとagents LLMを外部システムへ（出典）]]（学術講義, 2026-08-04。本文はユーザー提供の文字起こしを使用。※文字起こしは RAG の知識スペース構築の途中で切れており、tool calling/agents の本体は含まれない）

---

## 一行で

LLM コースの RAG 導入回。vanilla LLM の「学習カットオフ以降の出来事を知らない」「行動できない」2つの弱点のうち前者に焦点。GRPO の recap の後、RAG の必要性を3つの理由（コンテキスト長制限・性能劣化・コスト）で説得し、retrieve/augment/generate の3ステップを紹介。needle in a haystack テストのヒートマップが直感的。

## 前回の recap（reasoning model と GRPO）

- **vanilla LLM**：prompt → 直接 response
- **reasoning model**：prompt → reasoning chain（隠蔽）+ response。数学・コーディング等の推論タスクで性能向上
- **GRPO（Group Relative Policy Optimization）**：価値関数を訓練しない RL アルゴリズム。同じ prompt の複数 completion の報酬から相対的 advantage を計算
- **2つの報酬**：(1) reasoning chain を出力する報酬 (2) 良い response を出す報酬
- **length bias 問題**：訓練が進むと response が長くなり続ける（性能は頭打ちなのに）。GRPO の loss にトークンが短文か長文かで寄与が変わる項があるため
  - **double**：位置/文に依存しない正規化項
  - **GRU Don Rights**：正規化項を完全に除去

## 今回の2つのテーマ

1. **最新情報へのアクセス**（本講義の前半＝RAG）
2. **LLM に行動させる**（tool calling・agents＝後半だが文字起こしはここで切れている）

## RAG の必要性（3つの理由で「ナイーブに全部突っ込む」を否定）

ナイーブアプローチ：「カットオフ以降の情報を全部プロンプトに詰め込む」

| 反論 | 内容 |
|---|---|
| **1. コンテキスト長は有限** | 数十万トークン（GPT-5 で400k）。1トークン≈4文字なので数百ページ＝大分厚い本1冊分。全部は入らない |
| **2. 無関係情報で性能劣化** | 仮に無限でも問題。needle in a haystack テスト（下記）で実証 |
| **3. コストがトークン比例** | 1Mトークン≈1ドル。全部入れると毎プロンプトで高額 |

### 知識を再学習で注入するのもダメ

「カットオフ後に学習し直せばいいのでは？」への2つの反論：
1. **LLM の知識を回帰させずに変更するのは非常に難しい**（避けられるタスク）
2. **各ユースケースごとに fine-tune した重みを全部更新するのは非実用的**（保守オーバーヘッド甚大）

## needle in a haystack テスト（核心の可視化）

- **haystack**（干し草の山）= 巨大なプロンプト
- **needle**（針）= その中に隠した1つの事実
- モデルに「その事実は何だったか？」を問う

GPT-4 で実測したヒートマップ：
- X軸 = プロンプト長 / Y軸 = 事実の配置位置（文書 depth）
- **あるトークン数を超えると取得困難**になる
- 特に**前半に置かれた事実**の取得が悪い（loss-in-the-middle 現象）

→ コンテキストが無限でも無関係情報の増加が性能を劣化させる実証。

## RAG の3ステップ（Retrieve / Augment / Generate）

```
[質問] → [1. RETRIEVE: 知識スペースから関連文書を取得]
      → [2. AUGMENT: 質問 + 取得情報を統合したプロンプト構築]
      → [3. GENERATE: LLM が応答]
```

### 具体例（ローカル選挙）
- 質問：「この選挙の勝者は？」
- retrieve：選挙結果の文書を知識スペースから取得
- augment：「この選挙の勝者は？ちなみにこの選挙は〜で勝者は〜」に拡張
- generate：LLM が応答（答えをプロンプトに与えているので実質パラズフレーズ）

### retrieval ステップが最重要
- retrieve で間違った文書を取ると全体が失敗
- 本講義の焦点は「どう retrieval を良くするか」
- 評価方法についても後述（文字起こしはここで途切れている）

## 関連

- [[12_AIエージェントのメモリシステム 4層構造とRAG]] — RAG を semantic memory の取得機構として位置づけ。本講義はその RAG 自体の基礎
- [[02_GraphRAG 知識グラフでRAGを置き換える]] / [[10_マルチエージェントでナレッジグラフ構築 Neo4j×Google ADK]] — RAG の発展形。本講義の「retrieve を良くする」の究極系
- [[11_LLM数学基礎 トークン化からTransformerまで]] — LLM の構造。本講義の GRPO recap はその学習側の話
- [[09_ジェネレータ×エバリュエータハーネスで長時間走るエージェント]] — 「compaction ≠ 一貫性」は loss-in-the-middle の別形。無関係情報を詰め込むと劣化する本講義の指摘と同じ問題
- [[07_Boris対談 Anthropic内部Claude運用とエージェント設計]] — evals と vibes の使い分け。本講義の retrieval 評価も同種

## 所感

「全部プロンプトに詰め込む」ナイーブ案を3つの角度（長さ・性能・コスト）で否定し、RAG の必然性を説く構成が教育的に上手い。特に needle in a haystack のヒートマップは「コンテキスト窓が大きいから大丈夫」という素人想法を視覚的に覆す。本ボルトの運用でも、CLAUDE.md を巨大化させるのでなく「要約ページ（semantic）から必要なものだけ retrieve する」設計は RAG の原則に合致。文字起こしが知識スペース構築の途中で切れているのが残念（chunking/embedding/類似度計算等の実装詳細と、tool calling/agents の本体は次回以降と思われる）。
