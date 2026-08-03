---
title: "06_Tsumugu AI時代のZero Config ドキュメントサーバー"
tags: [summary, tool, tsumugu, documentation, ast, security, ai-workflow]
source: https://dev.classmethod.jp/articles/ai-era-docs-tsumugu/
author: おく（DevelopersIO）
published: 2026-08-03
created: 2026-08-03
---

# Tsumugu — AI 時代の Zero Config ドキュメントサーバー

> **Markdown・HTML・MDX・OpenAPI を設定ファイルなしで読めるドキュメントサイトに変換するツール。Semantic AST でフォーマット差を吸収し、「Content does not execute」のセキュリティモデルで書き手を信用しない。全コードを Claude Code/Codex で生成（930+テスト）。**

出典: [[72_Tsumugu AI時代のZero Config ドキュメントサーバー（出典）]]（おく, 2026-08-03。本文はクリップ全文を使用。Tsumugu は pre-alpha）

---

## 一行で

DevelopersIO のおく氏が作ったドキュメントサーバー。`npx tsumugu dev docs` だけで Markdown/HTML/MDX/OpenAPI を読めるサイトに。Semantic AST（意味の木）で全フォーマットを統一し、CSP +「書き手を信用しない」で実行しない。AI が書いたドキュメントを含めても安全。コードは1行も自分で書いていない（Claude Code/Codex 生成、930+テスト）。

## Zero Config 設計

設定ファイル（`tsumugu.config.ts`）を作らない。設定になりそうなものを全部よそへ散らす：

- ドキュメントルート → コマンド引数（省略時 `./docs`）
- タイトル/説明/並び順/非表示 → 文書自身の Front Matter
- サイト名 → トップページの title
- renderer/theme → preset（差し替えは TypeScript）
- ホスト/ポート/出力先 → コマンドフラグ

副産物：親ディレクトリの見覚えのない設定ファイルのせいで挙動が変わる事故が起きない。

## Semantic AST（核心アーキテクチャ）

全フォーマット（Markdown/HTML/MDX/OpenAPI）を入口で **Semantic AST** に変換し、以降は「その断片が何を意味するか」だけを扱う。

一方向パイプライン：Scanner（ファイル検索）→ Renderer（Semantic AST 変換）→ Transformer（AST 書き換え）→ Theme（Virtual Tree 組立）→ Serializer（HTML 出力）。上流へ戻らない。

- Semantic AST に `div`/`span`/`section` はなく、意味情報のみ。見た目は Theme の担当
- フォーマットを知っているのは Renderer まで。以降は元フォーマットを意識しない
- DOM そのままや Markdown AST に寄せると片方が二級市民になるので「どちらでもないもの」を挟む

OpenAPI の恩恵：tag→見出し、operation→HTTPメソッド+パスのセクション、パラメータ/レスポンス→テーブル、スキーマ→コードブロック。新しいノード型なしに API エンドポイントが目次/検索/`documents.json`/`llms.txt` に自動載り。Theme は OpenAPI を知らない。

**機械向け出力**（`documents.json`/`llms.txt`/`search.json`/`sitemap.xml`）はすべて Transformer 後の Semantic AST から生成。HTML をスクレイプしないので、テーマ差し替えで AI が読むテキストが変わらない。人間向けと AI 向けの別データ構造を持たない（ズレない）。

`hidden: true`：存在は `documents.json` にフラグ付きで入る（正直に答える）が、`llms.txt`/`sitemap.xml`/`search.json` からは外す（推薦と逆になる）。※アクセス制御ではなく一覧に出さない意味。

## セキュリティモデル：Content does not execute.

3者の信頼モデル（重要なのは真ん中）：

| Who | What |
|---|---|
| `tsumugu` を実行した人 | すべて |
| ドキュメントの書き手 | **文章だけ。マークアップやスクリプトは信用しない** |
| ポートへ到達できる誰か | 何も |

AI がドキュメントを書く時代、ドキュメントを実行するツールは書いた全員を Code Owner にしてしまう。

- HTML は Semantic AST へ変換、意味対応不能なマークアップはエスケープテキスト保持。`<script>` は Diagnostic 出して中身を落とす
- 生 HTML 出力の唯一の入口 `trustedHtml` は「なぜ信用できるか」を文字列で要求
- CSP は `default-src 'none'`、`script-src` は Tsumugu 自身のスクリプトの SHA-256 ハッシュのみ。`nonce`/`'self'` は使わない（書き手不信と噛み合わない）。1バイト違えば実行されない
- MDX は本物の MDX 構文としてパースするが**実行しない**（`{expression}`/`<Component/>`/`import`/`export` をそのまま表示）
- `--trust` は機能フラッグでなく Operator の「このルートは自分のもの」宣言。デフォルト OFF。信頼を決めるのは書き手でなく Operator

## 図は自分で描く

Mermaid をブラウザ配信（CSP違反）も jsdom 動かす（`foreignObject` 崩壊、177MB）も不可。`tsumugu-transformer-mermaid` としてサブセット（フローチャート `graph`/`flowchart` の TD/TB/LR/RL/BT とシーケンス図のみ）を自前パース→SVG 出力。インライン埋め込みで `currentColor` 追従、文字選択/検索可能。逃げ道：(1) `.svg` を `![]()` で参照、(2) `--trust` で本物の Mermaid/MDX。

## 検索（依存追加なし）

クライアント2.6KB/46行。フレームワーク/バンドラ/ビルドステップなし。部分一致（小文字化+NFKD）、あいまい検索しない。ランキング：全語一致 → セクション見出し > タイトル > 本文、語頭 > 語中。JS 無環境では `<form>` で全文書一覧ページへ（マッチング実装を2つ持たない）。

## ビルドパフォーマンス

| ドキュメント数 | 初回 | 再ビルド | 1ファイル編集 |
|---|---|---|---|
| 200 | 490ms | 20ms | 20ms |
| 1000 | 3.9s | 200ms | 140ms |

キャッシュ3層：文書（サイズ+更新時刻）、テーマ適用後本文/アウトライン（コンテンツハッシュ）、シリアライズ済みページ（外側依存の署名）。3層目導入前は1000ドキュメントで2.8秒かかっていた。

## AI で全コード生成

コードは1行も自分で書いていない（Claude Code/Codex）。時間を使ったのは仕様/設計/意思決定。流れ：`grill-with-docs`（要件）→ `to-spec`（仕様）→ `to-tickets`（Issue分解）→ `implement`（実装）。Matt Pocock の Skills をオーケストレーションスキルで連結。テストに時間をかける（930+テスト、examples/ を毎コミット配信、未載 Diagnostic コードでテスト落ち）。

レビュー対象がコードから成果物へ移っただけで、品質の責任まで AI へは渡せない。

## 関連

- [[01_agent-skill-creator ワークフローをAIスキル化]] / [[05_claude-code-prompt-improver 送信瞬間に前提を補完]] — AI ワークフロー/スキルの事例。Tsumugu も Matt Pocock Skills のオーケストレーションで構築
- [[02_Claude Code 計画と実行を分けるワークフロー]] / [[04_Stop Vibe Coding Spec駆動開発の5ブロック]] — Tsumugu の `grill-with-docs`→`to-spec`→`to-tickets`→`implement` は spec 駆動の実例

## 所感

「AI が書いたドキュメントを含めても安全」を実現する「書き手を信用しない」境界は、AI 時代特有の新しい設計軸。Semantic AST によるフォーマット統一と「Content does not execute」の組み合わせが、人間向け/機械向けの出力ズレとセキュリティを同時に解く。設定ファイルを作らない（Zero Config）思想も、[[08_AIフレンドリーなCLIを開発するテクニック]] の「help/docs コマンドで Web Fetch 不要」と共鳴。
