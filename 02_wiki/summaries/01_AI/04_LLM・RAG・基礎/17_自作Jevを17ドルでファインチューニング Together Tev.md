---
title: "17_自作Jevを17ドルでファインチューニング Together Tev"
tags: [summary, ai, jev, fine-tuning, together-ai, qwen, open-source, classification]
source: https://x.com/nutlope/status/2102881280115249597
author: nutlope（@nutlope / Together AI）
published: 2026-09-24
created: 2026-09-25
---

# 自作 Jev を17ドルでファインチューニング — Together Tev

> **Together AI が Jev ライク分類モデル together/Tev1-4B-experimental（Qwen3.5 4B ベース・オープンウェイト）を公開。8種の公開データセット37,840例で $17・数時間で自前ファインチューニング可能。JSON 入出力で state+question+options を渡す形式。専用エンドポイント（H100 1枚）でデプロイ。プライベートデータ・ベンダーロックインなし。**

出典: [[113_自作Jevを17ドルでファインチューニング Together Tev（出典）]]（@nutlope / Together AI, 2026-09-24。本文はクリップ全文を使用）

---

## 一行で

Together AI の nutlope による「自分の Jev を $17 で訓練する」ハウツー。Jev の System One 分類という概念を、オープンウェイトの Qwen3.5 4B で再現し自ドメイン向けに安く調整する道を示す。

## 構成（パイプライン）

| 工程 | 内容 |
|---|---|
| **データセット** | 8種・37,840例（MultiNLI/BoolQ/Banking77/AG News/SST-5 + Programmatic policies 13,500/Routing 6,000/Research taxonomy 3,840） |
| **正規化** | 全ソースを同一形式へ（fetch_sources.py → build_all.py） |
| **訓練** | Together Fine-tuning・Qwen3.5 4B ベース・**$17** |
| **デプロイ** | 専用エンドポイント（1x H100 80GB）・HTTP サーバー |
| **クエリ** | JSON（state + question + options） |

## 意義

- **Jev の概念の民主化**: TypeSafe Jev（クローズド・$0.042/MTok）に対し、**オープンウェイトで自前訓練・自前ホスト**が可能
- **$17 と数時間**の障壁の低さ — 分類モデルのドメイン特化が個人の手に
- **プライベートデータで訓練可能**（ベンダーにデータを送らない）・ロックインなし
- データセット構成が設計の見本: **NLI/真偽/意図/ニュース/感情（汎用判断）+ policies/routing/taxonomy（エージェント実務判断）**を混ぜる

---

## 本ボルト内の位置付け

- **Jev ノート群の「オープン替代」**: [[05_Jev Engineering 決定と生成を分離するSystem One]]（TypeSafe Jev 本体）・[[07_Jev Engineering実践ガイド 分割と失敗モード]]（「価格は補助金の可能性・自己運営ベンチマーク」等の警告）への直接的な回答 — **オープンウェイトで自前訓練すれば価格・ロックイン・ベンチマーク不信を回避できる**
- **@hkunimitsu の将来予測の裏付け**: [[08_Jevは誰のためのモデルか 汎用Decision Model]]「Jev 的機能は OpenAI/Google/Anthropic にも実装される」に対し、Together が既に実装・公開した実例
- **$17 ファインチューニング** は [[16_QWen Image Edit 軽量QLoRAでジブリ風画像生成]]（QLoRA 4bit で VRAM 削減）と同じ「軽量ファインチューニングの民主化」系譜。画像生成（QLoRA）と分類（SFT）で手法は違うが「ミドルクラス環境で自前モデル」の流れ
- **汎用判断+実務判断の混成データセット** は [[10_Memory Engineering 最も見過ごされる層]]（write policy=何をいつどんな形式で）と同じ「判断の設計」思想の訓練側表現
- **専用エンドポイント（H100 1枚）** は [[09_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤]] 等と同じ「自前インフラ」選択肢

---

## 関連

- TypeSafe Jev 本体 → [[05_Jev Engineering 決定と生成を分離するSystem One]]
- 失敗モード・価格警告（自前訓練の動機） → [[07_Jev Engineering実践ガイド 分割と失敗モード]]
- Jev の位置付け・将来実装予測 → [[08_Jevは誰のためのモデルか 汎用Decision Model]]
- 軽量ファインチューニング系譜 → [[16_QWen Image Edit 軽量QLoRAでジブリ風画像生成]]
- コンテキスト節約の実践 → [[14_Claude Code×Jev コンテキスト91%節約]]
