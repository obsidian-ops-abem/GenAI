---
title: "72_Tsumugu AI時代のZero Config ドキュメントサーバー（出典）"
tags: [raw-source]
source: https://dev.classmethod.jp/articles/ai-era-docs-tsumugu/
author: おく
published: 2026-08-03
created: 2026-08-03
---

# 出典メタデータ

- URL: https://dev.classmethod.jp/articles/ai-era-docs-tsumugu/
- 著者: おく（DevelopersIO）
- 公開: 2026年8月3日
- ツール: Tsumugu（pre-alpha、`npx tsumugu dev docs`）
- 実装: 全コード Claude Code と Codex で生成、930以上のテスト

---

# 本文

AI を使った開発では、以前にも増してドキュメントを書く機会が増えました。設計書や仕様書、調査メモ、ADR。AI に書かせたものも自分で書いたものも、コードと一緒に `docs` ディレクトリで管理しているプロジェクトは、もう珍しくないと思います。

ところが、ドキュメントが増えるほど「読む」体験は悪くなっていきます。欲しかったのは、Markdown でも HTML でも MDX でも、 `docs` に置くだけでそのまま読めるドキュメントサイトになるツールでした。

## docs に置くだけ

```bash
npx tsumugu dev docs
```

セットアップはこれで終わりです。ディレクトリの形がそのまま URL になります。

```
docs/
├── index.md               →  /
├── guide/
│   ├── index.md           →  /guide
│   └── getting-started.md →  /guide/getting-started
├── reference/api.html     →  /reference/api
└── images/diagram.svg     →  ドキュメントの隣で配信される
```

設定ファイルは最初から作るつもりがありませんでした。設定になりそうなものは全部よそへ散らしました：

- ドキュメントルート → コマンドの引数。省略したら `./docs`
- タイトル、説明、並び順、非表示 → その文書自身の Front Matter
- サイト名 → トップページの title
- renderer や theme の構成 → preset。差し替えるなら TypeScript で書く
- ホスト、ポート、出力先、origin → それを使うコマンドのフラグ

## Markdown も HTML も OpenAPI も、同じ AST にする

Tsumugu はフォーマットの違いを入口で吸収して、そこから先は「その断片が何を意味するか」だけを扱います。Markdown も HTML も MDX も OpenAPI も、いったん **Semantic AST** に変換してから流しています。

パイプラインは一方向：Scanner がファイルを見つけて `Document` を作り → Renderer が Semantic AST に変換 → Transformer が AST を書き換え → Theme が表示用の Virtual Tree を組み立て → Serializer が HTML を吐く。

フォーマットを知っているのは Renderer まで。Semantic AST には `div` や `span` のような見た目のための要素はなく、意味情報だけ。見た目は Theme の担当。

OpenAPI の場合：tag → 見出し、operation → HTTPメソッド+パスの見出しとセクション、パラメータ/レスポンス → テーブル、スキーマ → コードブロック。新しいノード型は足さず、API エンドポイントが普通のドキュメントと同じように目次へ載り、検索に引っかかり、`documents.json` や `llms.txt` にも入る。

機械向け出力（`documents.json`、`llms.txt`、`search.json`、`sitemap.xml`）はすべて Transformer 適用後の Semantic AST から生成。HTML をスクレイプしないのでテーマ差し替えで AI が読むテキストが変わらない。

`hidden: true` の文書は `documents.json` にはフラグ付きで入る（存在は正直に答える）が、`llms.txt`/`sitemap.xml`/`search.json` からは外す（推薦と逆になるため）。※`hidden` はアクセス制御ではなく一覧に出さないという意味。

拡張ポイントは5つ：renderer、transformer、theme、serializer、plugin。Core は OpenAPI にも Mermaid にも検索にもビルドにも依存しない。

## コンテンツは実行しない

セキュリティモデルは一文：**Content does not execute.**

3者の信頼モデル：

| Who | What |
| --- | --- |
| `tsumugu` を実行した人 | すべて。自分のマシンで自分が打ったコマンドなので |
| ドキュメントの書き手 | 文章だけ。マークアップやスクリプトは信用しない |
| ポートへ到達できる誰か | 何も |

重要なのは真ん中。ドキュメントを書くのは自分だけではありません（コントリビューター、vendoring、生成物、AI）。ドキュメントを実行するツールは、それを書いた全員を Code Owner にしてしまいます。

- HTML は Semantic AST へ変換され、意味に対応できないマークアップはエスケープ済みテキストとして保持。`<script>` は Diagnostic を出して中身を落とす。
- 生の HTML を出せる入口は `trustedHtml` 1つだけで、「なぜ信用できるのか」を文字列で渡す必要がある。
- すべてのレスポンスに `default-src 'none'` を基本とした CSP。`script-src` は Tsumugu 自身が生成したスクリプトの SHA-256 ハッシュのみ。`nonce` も `'self'` も使わない（書き手を信用しない前提と噛み合わない）。

MDX は本物の MDX 構文としてパースしたうえで**実行しない**：

| MDX の構文 | Tsumugu での扱い |
| --- | --- |
| `{expression}` | そのまま表示し、評価しない |
| `<Component />` | そのまま表示し、描画しない |
| `import` / `export` | そのまま表示し、実行しない |

`--trust` は機能フラグでなく「このドキュメントルートの中身は自分のもの」という Operator 自身の宣言。デフォルトは常に OFF。信頼を決めるのは書き手ではなく Operator。

## 図は自分で描くことにした

Mermaid をブラウザへ配る手は取れず（CSP）、jsdom 上で動かすと `foreignObject` が崩れる（177MB + ヘッドレスブラウザが必要）。なので `tsumugu-transformer-mermaid` として Mermaid 構文のサブセット（フローチャート `graph`/`flowchart` の TD/TB/LR/RL/BT とシーケンス図のみ）を自分でパースして SVG を吐く。

SVG はインライン埋め込みで `currentColor` に従いライト/ダークに追従。図の中の文字も選択・検索可能。`role="img"` と `aria-label` を付ける。

逃げ道2つ：(1) 図を `.svg` として書き出して `![alt](./pipeline.svg)` で参照、(2) `--trust` で HTML/MDX で書く。

## 検索は、依存を足さずに書いた

クライアント2.6KB、46行。フレームワークもバンドラもビルドステップもなし。マッチングは部分一致（小文字化+NFKD、結合文字を落とす）。あいまい検索はしない（名指しで探しているページが埋もれる）。

ランキング：クエリを空白で割って全語一致 → セクション見出し一致 > 文書タイトル > 本文、語頭一致 > 語中、同点なら文書順（1文書は12件中3件まで）。

JS無環境では `<form method="get" action="/search">` で全文書を並べるページへ飛ぶ（マッチング実装を2つ持つと食い違うため）。

## 保存してから画面が変わるまで

| ドキュメント数 | 初回ビルド | 再ビルド | 1ファイル編集 |
| --- | --- | --- | --- |
| 200 | 約490ms | 約20ms | 約20ms |
| 1000 | 約3.9s | 約200ms | 約140ms |

キャッシュ3層：読み込んだ文書（サイズ+更新時刻）、テーマ適用後の本文とアウトライン（コンテンツハッシュ）、シリアライズ済みページ（外側依存の署名）。

## 1行も自分で書いていない

コードは Claude Code と Codex に任せた。実際に時間を使ったのは仕様を考え、設計を見直し、意思決定をすること。流れ：

1. `grill-with-docs` で要件を洗い出す
2. `to-spec` で仕様へ落とす
3. `to-tickets` で Issue に分解する
4. `implement` で実装する

Matt Pocock さんの Skills をオーケストレーションスキルでつないだだけ。テストに時間をかけるように（930以上のテスト）。レビューの対象がコードから成果物へ移っただけで、品質の責任まで AI へ渡せるわけではない。

## おわりに

Tsumugu は pre-alpha（バージョン0から始まり、リリースごとに公開 API が変わる可能性あり）。
