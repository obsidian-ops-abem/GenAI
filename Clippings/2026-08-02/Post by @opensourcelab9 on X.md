---
title: "Post by @opensourcelab9 on X"
source: "https://x.com/opensourcelab9/status/2083681691537961218"
author:
  - "[[@opensourcelab9]]"
published: 2026-08-02
created: 2026-08-02
description: "【本命】 Claude Codeに雑に投げた一言を、送信の瞬間に「足りない前提」ごと渡し直すツールが★1831まで伸びてる。 入力を送った瞬間、3か所に割り込んで足りない条件を先に注ぐ ・曖昧な指示のときだけ1〜6個の質問を返し、明確な指示はそのまま素通しする ・実装や移行のよ"
tags:
  - "clippings"
---
【本命】

Claude Codeに雑に投げた一言を、送信の瞬間に「足りない前提」ごと渡し直すツールが★1831まで伸びてる。

入力を送った瞬間、3か所に割り込んで足りない条件を先に注ぐ👇

・曖昧な指示のときだけ1〜6個の質問を返し、明確な指示はそのまま素通しする

・実装や移行のような重い依頼では、先に進め方（分担するか自分でやるか）を決めさせる

・長く走るコマンドは裏で動かし、必要な出力だけ拾うよう指示が入る

・成果物が大きくなる回答には、結論先出し・表・簡潔さの型を差し込む

つまり

言葉を書き直させるのではなく、後から手で足すはずだった条件を最初から積んでおく。

一発目の質が上がるぶん、直しの往復が減る。

MITライセンスのPython製で、フック1個を入れるだけで今日から動く。

オープンソースはこちら👇

![画像](https://pbs.twimg.com/media/HOq5dO1agAAKmU-?format=png&name=large)

---

[github.com GitHub - severity1/claude-code-prompt-improver: Intelligent prompt improver hook for Claude Code....](https://t.co/2XNigbR4Ws)