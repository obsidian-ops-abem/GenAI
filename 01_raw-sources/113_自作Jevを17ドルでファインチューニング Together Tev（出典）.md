---
title: "113_自作Jevを17ドルでファインチューニング Together Tev（出典）"
tags: [raw-source]
source: https://x.com/nutlope/status/2102881280115249597
author: nutlope（@nutlope / Together AI）
published: 2026-09-24
created: 2026-09-25
---

# 出典メタデータ

- URL: https://x.com/nutlope/status/2102881280115249597
- 著者: nutlope（@nutlope・Together AI）
- 公開: 2026年9月24日
- 形態: X ポスト（ブログ記事紹介・コード付き）
- タイトル: **How to train your own Jev for $17**
- 対象: together/Tev1-4B-experimental（Qwen3.5 4B ベースの Jev ライク分類モデル）

要約は [[17_自作Jevを17ドルでファインチューニング Together Tev]] を参照。

---

# 原文（@nutlope の記事・構造化）

**TLDR**: Together AI は自社の Jev ライク分類モデル **together/Tev1-4B-experimental**（Qwen3.5 4B ベース）を公開。この記事では自分のバージョンのファインチューニング方法を示す。

Jev は state + 定義済み質問を渡すと、スコア・真偽値・多択回答を高速・超安価に返す分類モデル。実応用例: eコマースの返品自動判定、ML論文の分類、テキストの感情評価等。

## Picking datasets（データセット選定）

38,000問を各分類タイプに特化したデータセットからサンプル:

| ソース | 判断 | 訓練例数 |
|---|---|---|
| MultiNLI | 支持/矛盾/中立 | 5,000 |
| BoolQ | 文章による yes/no | 3,000 |
| Banking77 | 銀行インテント選択 | 3,000 |
| AG News | ニュース分類 | 1,500 |
| SST-5 | 感情レベル選択 | 2,000 |
| Programmatic policies | ルール適用 | 13,500 |
| Routing | ルール判断 | 6,000 |
| Research taxonomy | 論文分類 | 3,840 |
| **合計** | | **37,840** |

この規模の訓練は**約$17.0**。大規模データセットは高価で時間がかかるため抑える。

## Normalizing the data（正規化）

- `uv run python fetch_sources.py` でデータセット取得
- `uv run python build_all.py` でサンプルと正規化（全ソースを同じ形式へ）

## Training the model（訓練）

- Together AI の Fine-tuning サービスでジョブ起動: `uv run --with together --env-file .env python examples/train_together.py --launch`
- ベースは Qwen3.5 4B

## Deploying the model（デプロイ）

1. `tg fine-tuning retrieve ft-... --json | jq -r '.model_output_name'` でモデル名取得
2. 専用エンドポイントを作成:
```
tg endpoints create MODEL_OUTPUT_NAME --hardware 1x_nvidia_h100_80gb_sxm --display-name jev-v1-4b --wait
```
3. `.env` に `JEV_MODEL=<endpoint名>` を設定。HTTP サーバー経由でクエリ可能に

## Querying the model（クエリ）

JSON 入出力でファインチューニングされているため、質問と選択肢を JSON で構成:
```json
{
  "state": "Customer message: Hi, I checked my statement and your company charged my card twice...",
  "question": "Which listed support intent best matches this customer's message?",
  "options": [{"label": "A", ...}, ...]
}
```
テストケース一式がリポジトリに含まれ、返金二重請求の例等で意図分類を検証。

## Wrapping up

- Jev ライクな分類モデルは**自分のデータ・自分のドメイン向けに $17 と数時間で構築可能**
- オープンウェイト（Qwen3.5 4B ベース）なのでベンダーロックインなし・プライベートデータでも訓練可能
