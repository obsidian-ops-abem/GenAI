---
title: "Post by @beamnxw on X"
source: "https://x.com/beamnxw/status/2083539500165149086"
author:
  - "[[@beamnxw]]"
published: 2026-07-25
created: 2026-08-02
description: "この論文、めっちゃぶっ飛んでる コンピュータサイエンスの論文が、コーディングエージェントのための欠けていた実行レイヤーとしてループ仕様を定義している その結果：再利用可能なループ仕様は、明示的なトリガー、検証可能な目標、停止ルール、そしてメモリをバンドルして、手動のステップバ"
tags:
  - "clippings"
---
この論文、めっちゃぶっ飛んでる

コンピュータサイエンスの論文が、コーディングエージェントのための欠けていた実行レイヤーとしてループ仕様を定義している

その結果：再利用可能なループ仕様は、明示的なトリガー、検証可能な目標、停止ルール、そしてメモリをバンドルして、手動のステップバイステッププロンプティングを排除する

クレイジーなのは、ループエンジニアリングが人間の手取り足取りを自動化された検証ラダーで置き換える点だ

実世界のループの70％が、決定論的チェッカー、明示的な終端状態、そしてメイカー・クリティック分割を使って自律ゾーンで実行される

ほとんどの開発者は今でも、実行のあらゆるステップでコーディングエージェントを手動でプロンプトする

このフレームワークは、手動プロンプティングを境界付きの自己修正エージェントループで置き換える

完全な論文＋記事を以下で読む

将来の参考のためにブックマークしておけ

> **beamnxw ./ @beamnxw** · 2026-07-25
> 
> ![画像](https://pbs.twimg.com/media/HOo4IIuWsAAhLQ5?format=png&name=large) ![記事のカバー画像](https://pbs.twimg.com/media/HOAbmtOXYAAPWuL?format=jpg&name=large)

---

Paper:

https://arxiv.org/pdf/2607.00038v1…

![画像](https://pbs.twimg.com/media/HOo4I7OXIAAq0Au?format=png&name=large)

---

## Comments

> **unicode @unicodef1wn** · [2026-08-01](https://x.com/unicodef1wn/status/2083577216168825088)
> 
> beam found us a gem on arXiv, thanks

> **Sam @Systeo16** · [2026-08-01](https://x.com/Systeo16/status/2083546993679319199)
> 
> The "explicit stopping rule" part is the one I actually needed. Hermes would loop on a subtask, hit an ambiguous terminal state, and have no shared contract for what "done" looks like. Ended up writing it by hand as a ledger constraint. Didn't know there was a formal name for it.

> **Jurly @jurlycat** · [2026-08-01](https://x.com/jurlycat/status/2083546438831898918)
> 
> the loop spec angle is interesting but i wonder how it handles non-deterministic agent behavior in practice. specs assume a lot of control that LLMs just don't have yet.

> **Divyam @talwar\_divyam** · [2026-08-02](https://x.com/talwar_divyam/status/2083777995421159815)
> 
> Sol をクリア作業に使うのをやめてください。Luna は Codex クレジットが 25 倍少ない状態で実行できます。
> 
> アンロックは 3 プロファイルのルーターです：Luna ワーカー、Terra レビュー、曖昧さが支出を正当化する場合のみ Sol。
> 
> > **Divyam @talwar\_divyam** · 2026-08-01
> > 
> > Sol をクリア作業に使うのをやめてください。Luna は Codex クレジットが 25 倍少ない状態で実行できます。
> > 
> > アンロックは 3 プロファイルのルーターです：Luna ワーカー、Terra レビュー、曖昧さが支出を正当化する場合のみ Sol。
> > 
> > ![記事のカバー画像](https://pbs.twimg.com/media/HOp97CobcAAdh21?format=png&name=large)

> **Saeed Anwar @saen\_dev** · [2026-08-02](https://x.com/saen_dev/status/2083799130862739462)
> 
> Loop specs are a cleaner abstraction but they still assume you can define a verifiable goal upfront. For open-ended research tasks where you don't know what done looks like, how does the spec handle that, or is this mostly useful for deterministic workflows?

> **Miles S. @milessy\_bc** · [2026-08-01](https://x.com/milessy_bc/status/2083561638922277051)
> 
> most agent demos fall apart when nobody tells them when to stop

> **biektropic @biektive** · [2026-08-01](https://x.com/biektive/status/2083541443319156824)
> 
> every new agent paper is just another way of saying stop letting the model grade itself

> **sindikitil @sindikitil** · [2026-08-01](https://x.com/sindikitil/status/2083584004633276617)
> 
> This feels like the missing piece between planning and execution. Most agents still ping-pong between 'what next' instead of just looping until done.