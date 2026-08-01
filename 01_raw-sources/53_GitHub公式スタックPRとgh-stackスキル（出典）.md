---
title: "53_GitHub公式スタックPRとgh-stackスキル（出典）"
tags: [raw-source]
source: https://x.com/tonkotsuboy_com/status/2083462188640170237
author: tonkotsuboy_com (@tonkotsuboy_com)
published: 2026-08-01
created: 2026-08-01
---

# 出典メタデータ

- ポストURL: https://x.com/tonkotsuboy_com/status/2083462188640170237
- 著者: tonkotsuboy_com（@tonkotsuboy_com）
- 公開: 2026年8月1日
- 形態: X ポスト（図解紹介・日本語）
- 解説記事: https://zenn.dev/ubie_dev/articles/gh-stack-introduction

要約は [[13_GitHub公式スタックPRとgh-stackスキル]] を参照。

> [!info] 本文取得について
> 本記事はユーザーが Clippings フォルダへクリップした全文（日本語）を提供。図解4枚を含むが、本出典では図解の内容をテキストで要約。

---

# 本文（再構成）

## GitHub 公式のスタック型プルリクエスト

> GitHub公式から登場した**スタック型プルリクエスト（Stacked pull requests）**が解決する課題の図解🖼️

スタック PR は、**変更を依存関係のある一連の PR として積み重ねる**機能。1つの巨大な PR でなく、小さな PR の連鎖としてレビュー・マージできる。

## gh-stack スキルで自然言語操作

> 実際には **Claude Code や Codex 用スキル gh-stack** があるので、**自然言語でこれらのスタック PR を操れる**⛷️

GitHub 公式機能を直接操作するのでなく、**gh-stack という Agent Skill** を使えば自然言語でスタック PR の作成・管理ができる。解説記事: [Zenn — gh-stack introduction](https://zenn.dev/ubie_dev/articles/gh-stack-introduction)
