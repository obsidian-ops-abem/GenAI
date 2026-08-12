---
title: "96_Graph Engineering 11-Step Roadmap Obsidian→Fable5（出典）"
tags: [raw-source]
source: https://x.com/unicodef1wn/status/2080693998180606075
author: unicodef1wn (@unicodef1wn)
published: 2026-07-24
created: 2026-08-02
---

# 出典メタデータ

- ポストURL: https://x.com/unicodef1wn/status/2080693998180606075
- 著者: unicodef1wn（@unicodef1wn）
- 形態: X 長文ポスト（ノート）
- タイトル: **Graph Engineering: The 11-Step Roadmap From Obsidian Vault to Graph Fable 5 Can Actually Use**

## 概要

Obsidian vault が「ノートの山」だと、Fable 5 が質問のたびに全ノート（数千ファイル・数十万トークン）を読んでしまう。Graph Engineering は vault 内に router・index・nodes・edges の構造を構築し、2〜3ファイルの読み込みで回答可能にする手法。11ステップのロードマップ。

### 構造（vault レイアウト）

```
vault/
├─ ROUTER.md    # 毎セッション冒頭で読む、500トークン未満に抑える
├─ index.md     # ノート1行: 名前・リンク・一文説明
├─ nodes/       # ノート（1ファイル1アイデア）
├─ routines/    # 保存方法・検索方法
└─ state.md     # セッション間で維持する状態
```

### 3段階モデル

- **Pile（山）**: 構造なし。毎回全件スキャン
- **Graph**: router + index + edges。2〜3ファイルで回答
- **System**: graph + 複利する記憶。毎セッション賢くなる

### 設計原則

- Router は「地図」であって「マニュアル」ではない。事実と方向のみ、500トークン未満
- Index は1ノード1行。新規ノートは自動で1行追加
- Node は1ファイル1ジョブ。小さく特定化
- Obsidian の graph view は「人間用のポスター」。Fable 5 用は「エンジン（index）」

---

## 関連（ボルト内）

- Graph Engineering 全般 → [[05_Graph Engineering How to Stop Building AI Agents That Wait in Line（出典）]]
- 14-Step roadmap → [[43_Graph Engineering with Claude 14-Step roadmap（出典）]]
- Graphify（コードベースを知識グラフ化） → [[38_graphify コードベースを知識グラフ化（出典）]]
