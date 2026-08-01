---
title: "54_YC QM マルチプレイヤーエージェントハーネス（出典）"
tags: [raw-source]
source: https://x.com/ycombinator/status/2083243960684908768
repo: https://github.com/yc-software/qm
author: Y Combinator (@ycombinator)
published: 2026-08-01
created: 2026-08-01
---

# 出典メタデータ

- ポストURL: https://x.com/ycombinator/status/2083243960684908768
- GitHub: https://github.com/yc-software/qm
- 著者: Y Combinator（@ycombinator）— yc-software
- 公開: 2026年8月1日
- 形態: X ポスト（OSS 公開アナウンス・日本語）＋ コメント欄
- ライセンス: MIT
- 類似: Hermes・OpenClaw・Funky

要約は [[04_YC QM マルチプレイヤーエージェントハーネス]] を参照。

> [!info] 本文取得について
> 本記事はユーザーが Clippings フォルダへクリップした全文（日本語＋英語コメント）を提供したため、それに基づき転記。

---

# 本文（再構成）

## YC が社内ハーネスを OSS 化

> 私たちは、**YCで内部的に使用しているマルチエージェントのハーネス**をオープンソース化することを決めました。

**「QM」と呼ぶ**。Hermes や OpenClaw のようにカスタマイズしやすいものを意図しているが、**会社全体で役立つ**もの。会計・法律・イベント・エンジニアリング（QM 自体の構築を含む）全体で使用している。

- **MIT ライセンス**
- **クラウドファースト**
- **Slack とウェブ UI をネイティブに備える**

## 機能

- **トリガー**（cron・ウェブフック）・メモリ・共有ファイル
- **企業ブレイン用のコネクタ**
- **エージェントブラウザサポート**
- **共有可能なウェブアプリアーティファクト**
- **マルチプレイヤープロジェクト**

GitHub: [yc-software/qm — Multiplayer agent harness for work](https://github.com/yc-software/qm)

## コメント欄の洞察

- **@gpolzer**（Georg Polzer）: メトリクスのオントロジーを QM に使うべき —— 信頼できる答えを正しいソースから引けるように（GMV をランダムな Excel から引かないよう）
- **@binsquares**（BinBin）: 小さなマシンで QM をホストし公開試験。「webアプリをデプロイして」と頼むと自律的に構築・デプロイし共有リンク付きで
- **@chetan_guevara**（Chetan Nandakumar）: 同週に「OpenClaw for teams」（会社のブレイン・完全 OSS・MIT）をリリースしたと返信
- **@XiJin12**（Jason Jin）: アーキテクチャ図を確認 —— 「**decouple the brain and hands（脳と手を分離）**」戦略を採用。自身は Funky（durable runtime for agent swarms）を開発中
