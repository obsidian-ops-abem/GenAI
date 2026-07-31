---
title: 03_Mastra Trace Intelligence
tags: [記事まとめ, mastra, observability, ai-agents, tracing]
source: https://x.com/calcsam/status/2082160518115254519
author: Sam Bhagwat (@calcsam)
published: 2026-07-28
created: 2026-07-30
status: 未着手
---

# Mastra Trace Intelligence

> [!info] 出典
> Sam Bhagwat([@calcsam](https://x.com/calcsam)) 2026-07-28 投稿（Mastra共同創業者）
> [元ポスト](https://x.com/calcsam/status/2082160518115254519) / [発表記事](https://mastra.ai/blog/announcing-trace-intelligence)

## 一行で言うと

AIエージェントの実行履歴（トレース）を UMAP + HDBSCAN で自動クラスタリングし、goal/behavior/sentiment/outcome の4シグナルで共通パターンを可視化する Mastra の観測性機能。トレース手動レビューに「数週間〜数ヶ月」かかる問題を、優先順位付けされた開発ループに変える。

## 解こうとしている問題

エージェントを実運用する際、精度保証が極めて困難。チームがユーザートレースを確認して改善点を見つけるまでに **weeks or months** かかる。個別トレースを1件ずつ読むアプローチはスケールしない。

## 仕組み（技術スタック）

```
トレース完了
  ↓
1. メタデータ付きコンパクト表現を抽出 → シグナル生成
  ↓
2. 各シグナルを埋め込み（ベクトル化）
  ↓
3. UMAP で次元削減
  ↓
4. HDBSCAN で密度ベース・クラスタリング（類似シグナルをグループ化）
  ↓
5. テーマ量・トレンド・代表トレースを時系列表示
```

- **UMAP** — 高次元ベクトルを2〜3次元に圧縮し、可視化・クラスタリングしやすくする多様体学習の次元削減手法
- **HDBSCAN** — 密度に基づく階層的クラスタリング。クラスタ数を事前指定せず、密度の高い領域を自動的にクラスターとして抽出

## 識別する4つのシグナル

各トレースから4軸で特徴を抽出し、類似性で分類する：

| シグナル | 意味 |
|---|---|
| **goal** | ユーザーの目的 |
| **behavior** | エージェントの行動 |
| **sentiment** | 感情・満足度 |
| **outcome** | 最終的な結果 |

これにより「同じ目的で異なる結果に至った群」「同じ行動パターンで失敗した群」など、全体傾向を横断的に把握できる。

## Mastra platform での位置づけ

Mastra の **observability** 機能の一部として組み込み（別売り OSS の繋ぎ合わせではない）。3月リリースのデータセット・実験機能と合わせて、エージェントの学習サイクル（観測→仮説→実験→改善）を完成させるステップ。今後は実験フローの自動化を計画。

### Mastra プラットフォーム構成

| 層 | 役割 |
|---|---|
| Framework | エージェント・ワークフロー構築の基盤 |
| **Observability** | Metrics / logs / traces の管理・監視（**本機能はここ**） |
| Studio | 評価・コラボレーション環境 |
| Server | クラウドへのデプロイ |

## 提供形態・価格

- **ベータ版**（2026-07-27〜）。サインアップ: mastra.ai/trace-intelligence
- 価格：**Starter は無料・pay-as-you-go**、Teams は大きな許容量と低い使用率
- リージョン：**EU / US** にピン留め可能

## 所感・留意点

- **技術の新規性**より**統合の新規性**。UMAP+HDBSCAN は既存手法だが、エージェントトレースという新領域に「4シグナル抽出→埋め込み→クラスタリング→時系列」というパイプラインを適用し、プラットフォームに直接組み込んだ点が主張
- 汎用 LLM観測性ツール（LangSmith / Langfuse / Arize 等）と比較される位置づけ。差別化は「プラットフォーム直結」と「4シグナル自動分類」
- **[[08_Context Engineering Claude Codeの文脈設計]]** で述べられた Distraction/Confusion モードの「実際に起きているか」を、トレース群から定量的に把握するツールと言える。観測して初めて文脈設計の効果が測れる
- クラスタリングの精度・4シグナルの妥当性は、実際のベータ利用による外部検証が必要。発表時点では事例・数字の外部検証なし
- Mastra 自体は OSS（[mastra-ai/mastra](https://github.com/mastra-ai/mastra)）だが、Trace Intelligence を含む observability はプラットフォーム（マネージド）側の機能

## 次にやること

- [ ] ベータに申し込んで実際のクラスタリング精度を確認するか判断
- [ ] 既存のトレース観測アプローチ（LangSmith/Langfuse）との比較ページを作るか検討
- [ ] エージェント評価パイプライン（観測→実験→改善）をこのボルトの Lint プロセスに類推できないか検討

関連: [[00_インデックス]] / [[08_Context Engineering Claude Codeの文脈設計]]
