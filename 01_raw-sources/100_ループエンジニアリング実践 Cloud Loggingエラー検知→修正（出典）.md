---
title: "100_ループエンジニアリング実践 Cloud Loggingエラー検知→修正（出典）"
tags: [raw-source]
source: https://zenn.dev/tetsu_don/articles/e40b95dfc726ac
author: てつどん (tetsu_don)
published: 2026-08-02
created: 2026-08-02
---

# 出典メタデータ

- 記事URL: https://zenn.dev/tetsu_don/articles/e40b95dfc726ac
- 著者: てつどん（tetsu_don）— データエンジニア
- 形態: Zenn 記事
- タイトル: **Claude Code で「ループエンジニアリング」を実践してみた**

## 概要

Addy Osmani 氏が2026年6月に命名した「Loop Engineering」（エージェントにプロンプトを打つ人であることをやめ、代わりにそれを行うシステムを設計する）を実践した記録。

「Cloud Loggingのエラーを検知→原因調査→仕様書・コード修正→レビュー→push」というフローをAIエージェントに自律的に繰り返させる仕組みを構築し、**本物のバグを検出・修正するところまで実践**。

### 2種類のAI活用の区別

- **① AIを使ったバッチ処理**: コードを定期実行しAPIを1回呼ぶ。主導権は「人間が書いたコード」
- **② ループエンジニアリング**: Claude Code自身が「何を直すか・どう直すか・pushするか」を全部自律判断。主導権は「AIエージェント自身」

### Maker-Checker パターン

- LLMは「合格させたい」方向に評価を歪める傾向
- 対策: モデルが言い逃れできない決定的なゲート（pytest / mypy 等の自動判定）をループに置く
- 実装エージェント（Maker）と検証エージェント（Checker）を**完全に別のコンテキスト**で動かす
- Claude Code の**サブエージェント**機能が fresh context（記憶を共有しない）を自然に実現

### 設計方針（Skills）

1. 「High指摘が0件」になるまで繰り返す（終了条件）
2. 「要確認」のHigh指摘が1件でもあれば即座にループ中断・人間にエスカレーション
3. 上限回数を設ける
4. サブエージェント（reviewer）は「判定のみ」、呼び出し元が「修正」を行う

---

## 関連（ボルト内）

- Loop Engineering の理論 → [[29_Loop Engineering Claude,GPT 実戦で効くもの（出典）]]
- 14-step roadmap → [[10_Loop engineering 14-step roadmap（出典）]]
- 自己レビューエージェントのGraph設計 → [[48_自己レビューエージェントのGraph設計 Anthropicメソッド（出典）]]
