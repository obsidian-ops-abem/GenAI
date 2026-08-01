---
title: "Post by @ycombinator on X"
source: "https://x.com/ycombinator/status/2083243960684908768"
author:
  - "[[@ycombinator]]"
published: 2026-08-01
created: 2026-08-01
description: "私たちは、YCで内部的に使用しているマルチエージェントのハーネスをオープンソース化することを決めました。 これを「QM」と呼び、HermesやOpenClawのようにカスタマイズしやすいものにすることを意図していますが、会社全体で役立つものです。私たちはこれを会計、法律、イベン"
tags:
  - "clippings"
---
私たちは、YCで内部的に使用しているマルチエージェントのハーネスをオープンソース化することを決めました。

これを「QM」と呼び、HermesやOpenClawのようにカスタマイズしやすいものにすることを意図していますが、会社全体で役立つものです。私たちはこれを会計、法律、イベント、エンジニアリング（QM自体の構築を含む！）全体で使用しています。

このプロジェクト全体はMITライセンスの下にあります。クラウドファーストで、SlackとウェブUIをネイティブに備えています。

![画像](https://pbs.twimg.com/media/HOkdhV_bQAAOEJ7?format=jpg&name=large)

---

いくつかの機能：

• トリガー（cron、ウェブフック）、メモリ、共有ファイル

• 企業ブレイン用のコネクタ

• エージェントブラウザサポート

• 共有可能なウェブアプリアーティファクト

• マルチプレイヤープロジェクト

[github.com GitHub - yc-software/qm: Multiplayer agent harness for work](https://t.co/yKmDDWRIcX)

---

## Comments

> **Georg Polzer @gpolzer** · [2026-08-01](https://x.com/gpolzer/status/2083460995138126296)
> 
> Great! Make sure QM is using a metrics ontology for reliable answers about metrics from the right source - e.g. http://duodata.ai. You don’t want it to make up “GMV of summer 2025 batch” etc and then pull it from a random Excel file (instead of the right table / column in

> **David Nintang @davidnintang** · [2026-07-31](https://x.com/davidnintang/status/2083274550519955716)
> 
> これが新しいパラダイム…？ めっちゃ好き！
> 
> ![画像](https://pbs.twimg.com/media/HOlHKY8awAA46ZD?format=jpg&name=large)

> **BinBin @binsquares** · [2026-07-31](https://x.com/binsquares/status/2083260021098570224)
> 
> このメッセージを再投稿します：
> 
> ここでQMを小さなマシン上でホストするようになりました。誰でも試せるように！：
> 
> \- https://qm2-77c96774dcd9.apps.smolmachines.com
> 
> 「webアプリをデプロイして」と頼んでみてください。 それが自律的にこれを構築・デプロイし、共有できる公開リンク付きで：
> 
> \- …https://qm-deploy-d8d39343-64e-77c96774dcd9.apps.smolmachines.com
> 
> ![画像](https://pbs.twimg.com/media/HOk5uRraMAAoQfl?format=jpg&name=large)

> **saurabh @unfoundbox** · [2026-07-31](https://x.com/unfoundbox/status/2083280055502021056)
> 
> @grok http://buzz.xyz との機能パリティはどうなっていますか？

> **Chetan Nandakumar @chetan\_guevara** · [2026-08-01](https://x.com/chetan_guevara/status/2083367548008214549)
> 
> Amazing! We've just released "OpenClaw for teams" this week, a company brain that's fully open source. MIT licensed.
> 
> [aiosbrain.dev AIOS — The operating system for teams of humans and agents](https://t.co/7dFw9BmX77)

> **Jason Jin @XiJin12** · [2026-07-31](https://x.com/XiJin12/status/2083333026994622818)
> 
> Nice project! I checked the architecture diagram, and it is also adopting the "decouple the brain and hands" strategy.
> 
> BTW, I am currently working on Funky. It's also an multi-agent harness that emphasizes durability and decoupling. Open sourced at:
> 
> [github.com GitHub - funkyhq/funky: The durable runtime for agent swarms.](https://t.co/XMdNJQklHg)

> **Jon @latteliem** · [2026-07-31](https://x.com/latteliem/status/2083309703011140051)
> 
> ここに私の考えを書きました！
> 
> > **Jon @latteliem** · 2026-07-31
> > 
> > I just went through YC’s QM repo. @ycombinator @garrytan
> > 
> > My Conclusion? The Hard Part of Company-Wide Agents Is Not the Model
> > 
> > Most conversations around agents begin with model choice, choosing between larger context windows, better reasoning, faster tool use or even more