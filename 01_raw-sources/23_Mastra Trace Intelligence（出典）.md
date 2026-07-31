---
title: "Mastra Trace Intelligence（出典）"
tags: [raw-source]
source: https://x.com/calcsam/status/2082160518115254519
author: Sam Bhagwat (@calcsam)
published: 2026-07-28
created: 2026-07-30
---

# 出典メタデータ

- ポストURL: https://x.com/calcsam/status/2082160518115254519
- 実体記事: https://mastra.ai/blog/announcing-trace-intelligence
- 著者: Sam Bhagwat（@calcsam）— Mastra 共同創業者
- 公開: 2026年7月28日（ポスト）、ブログ発表は7月27日
- 形態: Xポスト（製品発表）+ Mastra Blog の発表記事
- ベータサインアップ: mastra.ai/trace-intelligence

要約は [[03_Mastra Trace Intelligence]] を参照。

---

# ポスト本文

Today we're incredibly excited to launch Trace Intelligence.

Teams shipping agents can spend months reviewing traces. So we auto-group traces into clusters and identify common patterns.

Watch how your agent is actually doing:

---

Trace Intelligence is built into observability on the Mastra platform.

Read more and sign up for beta:

---

# 実体記事本文（Announcing Trace Intelligence | Mastra Blog）

## Trace Intelligenceとは

エージェントの実行履歴（トレース）をクラスター（グループ）にまとめる機能。"grouping traces into clusters so you can identify common goals, behaviors, sentiments, and outcomes across many runs."（多くの実行履歴において共通の目標、行動、感情、結果を特定できるようにする）。

## 解決する課題

エージェント開発において、実運用に耐えうる精度を保証することは非常に困難。チームがユーザートレースを確認して改善点を見つけ出すまでに "weeks or months"（数週間から数ヶ月）を費やす。トレースの確認と修正の優先順位付けを支援し、開発ループを効率化する。

## 仕組み（クラスタリング技術）

1. 完了したトレースからメタデータと共に**コンパクトな表現**を抽出し、目標や行動などの**シグナル**を生成
2. 各シグナルを**埋め込み（ベクトル化）**
3. 次元削減に **UMAP** を使用
4. 縮小された空間での密度に基づくクラスタリングに **HDBSCAN** を用いて類似シグナルをグループ化
5. テーマの量やトレンド、代表的なトレースなどを**時系列で表示**

## 識別できる4つのシグナル

- **goal** — ユーザーの目的
- **behavior** — エージェントの行動
- **sentiment** — 感情や満足度
- **outcome** — 最終的な結果

## Mastra platform / observability の位置づけ

Mastra プラットフォームの "observability" 機能の一部として直接組み込み。3月にリリースされたデータセットや実験機能と合わせて、エージェントの学習サイクルを完成させるステップ。今後は実験フローの自動化も計画。

Mastra プラットフォーム構成：
- **Framework** — エージェントやワークフローを構築する基盤
- **Observability** — Metrics / logs / traces を管理・監視（Trace Intelligence はここに属する）
- **Studio** — 評価やコラボレーションを行う環境
- **Server** — エージェントをクラウドにデプロイ

## ベータ情報・価格・リージョン

- 本日より**ベータ版**提供
- 価格：Starter は無料・pay-as-you-go、Teams はより大きな許容量と低い使用率
- リージョン：EU / US リージョンにピン留め可能
- サインアップ：mastra.ai/trace-intelligence
