---
title: "55_AIフレンドリーなCLIを開発するテクニック（出典）"
tags: [raw-source]
source: https://zenn.dev/shunsuke_suzuki/articles/make-cli-ai-friendly
author: shunsuke_suzuki
published: 2026-08-01
created: 2026-08-02
---

# 出典メタデータ

- 記事URL: https://zenn.dev/shunsuke_suzuki/articles/make-cli-ai-friendly
- 著者: shunsuke_suzuki（Zenn）— aqua/pinact/tfcmt/ghalint/ghir/ghtkn 等 OSS CLI を公開
- 公開: 2026年8月1日
- 形態: Zenn 記事（技術解説・コード例）
- 対象 OSS: ghtkn v0.3.5（ただし内容は ghtkn に限らず一般的な CLI 開発に当てはまる）

要約は [[15_AIフレンドリーなCLIを開発するテクニック]] を参照。

> [!info] 本文取得について
> 本記事はユーザーが Clippings フォルダへクリップした全文を提供したため、それに基づき転記。

---

# 本文（再構成）

## 課題: AI は知らない OSS の知識を持たない

昨今 AI が CLI を扱うことも増えているが、それなりに知られた OSS でない限り AI はその OSS に関する質問やトラブルシューティングの知識を持たない。Coding Agent は Web Fetch するが、**闇雲に Web Fetch は効率が悪く、Web Fetch だとコンテンツが要約されてしまう**問題がある。

> AI に開発させるのが当たり前になってきた現在では、CLI を開発する際、**如何に AI にその CLI に関する知識を持たせるか**が重要になる。

ghtkn では Claude や Codex のような coding agent が Web Fetch せずにドキュメントにアクセスできるよう、AI フレンドリーにする様々な工夫を行っている。

## 1. CLI の help やログに agent 向けのメッセージを含める

ghtkn では以下のようなメッセージを含める:

### (1) docs コマンドへの導線
> If you are a coding agent, run 'ghtkn docs list' to list the documentation and 'ghtkn docs show \<doc>' to read it before answering questions about ghtkn or troubleshooting its errors.

### (2) 人間による操作が必要なコマンドは人間に実行してもらうよう案内
> The Device Flow is interactive and can't be completed by a background or non-interactive process. If you are a coding agent, do NOT run `ghtkn get` yourself because it would fail the same way; instead, ask the user to run `ghtkn auth` in their own interactive terminal to authenticate

### (3) secret の漏洩の注意喚起
> The output is a secret. Do not print, echo, log, or include it in a chat message, a commit, or any other output... 'GH_TOKEN=$(ghtkn get) gh issue list'. Better still, avoid handling the raw token at all - for git, use the credential helper ('ghtkn git-credential')...

人間なら読まずにスルーするようなログでも agent は読んでくれる（こともある）ので、**詳細なログ出力がより重要**になっている。

## 2. ドキュメントの編成

- ドキュメントを CLI のコードと**同じリポジトリ**で管理し、topic ごとに分割
- markdown で記述し、**YAML Frontmatter で description を記述**
- この description は **Agent Skill の description と同じように agent 向けに記述**
- 人間向けドキュメントと skill を別管理せず**共通化**してメンテナンスを容易に
- 本文は**人間と agent 両方が読みやすいように**書く

## 3. docs コマンドの提供

ghtkn では `ghtkn docs list` と `ghtkn docs show <name>` を提供:
- coding agent が Web Fetch せずにドキュメントにアクセスできる
- ドキュメント一覧を表示し、個別ドキュメントを表示
- agent が必要な情報を自律的に取得できる

## 4. Auth コマンドの設計

Device Flow（インタラクティブ）は agent が実行できない:
- `ghtkn auth` を人間が実行し、token を保存
- agent は保存された token を使う（`ghtkn get` で取得、credential helper で git に統合）
- **インタラクティブな操作は人間、非インタラクティブな操作は agent** と責務分離

## 5. 出力フォーマット

- `--format json` 等の構造化出力で agent が解析しやすく
- secret の扱い（生 token を露出させず credential helper や環境変数で）

## 結論

CLI 開発において **AI に知識を持たせる設計**が重要:
- help/ログに agent 向けメッセージ
- docs コマンドで Web Fetch 不要
- Auth の人間と agent の責務分離
- ドキュメントと skill の共通化
- 構造化出力
