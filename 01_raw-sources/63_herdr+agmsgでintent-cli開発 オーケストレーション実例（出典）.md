---
title: "63_herdr+agmsgでintent-cli開発 オーケストレーション実例（出典）"
tags: [raw-source]
source: https://x.com/tomohisa/status/2083660531886706743
author: tomohisa（@tomohisa）
published: 2026-08-02
created: 2026-08-02
---

# 出典メタデータ

- URL: https://x.com/tomohisa/status/2083660531886706743
- 著者: tomohisa（@tomohisa）
- 公開: 2026年8月2日
- 形態: X ポスト（動画付き・実開発風景）
- タイトル: **herdr+agmsg を使ったオーケストレーションで intent-cli 自身を開発している様子**

要約は [[05_herdr+agmsgでintent-cli開発 スレッド分離オーケストレーション]] を参照。

---

# 原文（@tomohisa の X ポスト全文）

herdr+agmsgを使ったオーケストレーションで、intent-cli自身を開発している様子です。

人間と話すのはClaudeデスクトップアプリ内の設計スレッドで、herdr内に、オーケストレーションスレッド、設計スレッド、レビュースレッドを置いて、ビデオ内では、レビューがrequest-updateでPRに問題指摘して、実装が修正をし始めるまでの状況を示しています。

設計スレッドは作業の指示、設計をするだけなので、いつでも追加のタスクなどについて会話することができます。
