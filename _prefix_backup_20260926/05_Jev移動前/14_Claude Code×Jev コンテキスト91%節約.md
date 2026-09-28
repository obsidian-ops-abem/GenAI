---
title: "14_Claude Code×Jev コンテキスト91%節約"
tags: [summary, ai, jev, claude-code, context-engineering, compaction, cost]
source: https://x.com/yama_threads/status/2102260578521645349
author: yama（@yama_threads）
published: 2026-09-22
created: 2026-09-24
---

# Claude Code×Jev — コンテキスト91%節約

> **「要約」と「選別」の違いが全て。従来の compaction は要約プロンプト（=生成・遅い・欠落リスク）。Jev はエントリごとに関連度をスコアリングし無関係なものを削る関連性フィルター（=判断・1秒）。「コンテキストを後から掃除するのでなく最初から散らかさない」。修正対象はモデルでなく state。圧縮より先に2層防御（ルータ+ゲート）。**

出典: [[112_Claude Code×Jev コンテキスト91%節約（出典）]]（@yama_threads, 2026-09-22。本文はクリップ全文を使用。※記事中盤に Threads×AI 収益化の宣伝を含むが技術内容は Jev Engineering ガイドのコンパクション解説）

---

## 一行で

@yama_threads による Jev のコンパクション利用に絞った日本語ハウツー。Alex Volkov 実測「約100万トークン→86K を1秒（91%削減）」を入口に、要約と選別の違い・3つの導入ルート・2層防御・落とし穴を整理。

## 核心 — 要約 vs 選別

| | 要約（従来） | 選別（Jev） |
|---|---|---|
| やること | 文章を書き直す | 残すか捨てるかを決める |
| 処理 | **生成**（1分） | **判断**（1秒） |
| リスク | 情報欠落 | 削られた前提は state で明示的に渡し直す設計が必要 |

> "Compaction is not a summarization prompt. It is a relevance filter."
> **コンテキストを後から掃除するのではなく、最初から散らかさない**

## 導入3ルート

1. **Playground で1問**（Noul: 「このエントリはゴールに関連するか？」）— コード不要・今日やるならこれ
2. **外部スクリプト手動**（Score で閾値未満を削除・書き戻し）
3. **Hook 自動化**（一定量超過で発火。閾値厳しすぎると前提情報まで落ちる）

> 回答がおかしい時の修正対象は**モデルでなく state**（"the fix is almost always the state, not the model"）

## 圧縮より先に — 2層の防御

- **上層ルータ**: タスク→適切なモデルへ振り分け
- **下層ゲート**: 危険ツール実行を事前ストップ（rm -rf・force push・本番接続文字列）
- 「安全か？」の判定は生成でなく判断 → 高価な LLM 不要
- **コンテキストに入ってから削るより、入る前にゲートで弾く方が事故コストは圧倒的に小さい**

## 落とし穴（正直な注意）

- **定額プランの「節約」=使用枠の温存と速度向上で、月額請求は変わらない**
- **Jev 側の利用料は別途発生**（トータル損得は利用量次第）
- 「1秒で86K」は報告事例であり万能数字でない
- 選別で落ちた情報は state への明示的な再供給設計が必要

---

## 本ボルト内の位置付け

- **[[05_Jev Engineering 決定と生成を分離するSystem One]] のユースケース5（インスタント・コンパクション）の日本語実践解説版**。1M→86K・1秒の数字の出所解説と、定額プラン用户視点の注意（金銭節約でない）が差分
- **「要約でなく関連性フィルター」** は [[06_Context Engineering Claude Codeの文脈設計]]（削除優先・段階的開示）・[[05_Claude Codeの6層アーキテクチャ ダムループ]]（95%到達で要約でなく構造化抽出・**pruning beats summarizing**）の Jev 実装。本ボルトの Lint/Ingest も「要約でなく構造化抽出（frontmatter・Wikilink・index）」を保つ方針と同一線
- **「最初から散らかさない」** は [[10_Memory Engineering 最も見過ごされる層]]（write policy beats bigger windows）と同根 — 書く段階で S/N を保つ
- **「修正は state でなくモデル」** は [[11_Thoric講演 Fableフィールドガイド unhobblingとunknowns]]（blind spot pass・ハーネスは理解の関数）と同じ「入力設計が決める」思想
- **2層防御（ルータ+ゲート）** は [[05_Jev Engineering 決定と生成を分離するSystem One]]（ハーネス2層）・[[04_Claudeはorchestrator専念 hook強制の分業]]（hook で構造的強制）と同型
- **「削られた前提の明示的再供給」** は本ボルトの index.md（working memory）→ Wikilink（1 hop）→ 全文の階層検索（[[10_Memory Engineering 最も見過ごされる層]] の hierarchical retrieval）と同じ課題

---

## 関連

- Jev ガイド（出所） → [[05_Jev Engineering 決定と生成を分離するSystem One]]
- 失敗モードと実務 → [[07_Jev Engineering実践ガイド 分割と失敗モード]]
- Jev の位置付け → [[08_Jevは誰のためのモデルか 汎用Decision Model]]
- 削除優先・段階的開示 → [[06_Context Engineering Claude Codeの文脈設計]]
- pruning beats summarizing → [[05_Claude Codeの6層アーキテクチャ ダムループ]]
- write policy → [[10_Memory Engineering 最も見過ごされる層]]
- hook 強制 → [[04_Claudeはorchestrator専念 hook強制の分業]]
