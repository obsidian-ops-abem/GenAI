---
title: "The self-improving AI trading machine is mostly built. One piece left."
source: "https://x.com/antpalkin/status/2085431604906766385"
author:
  - "[[@antpalkin]]"
published: 2026-08-07
created: 2026-08-08
description: "A trading strategy loses money in testing. Instead of getting deleted, it reads its own autopsy, finds the exact place it bled, rewrites its..."
tags:
  - "clippings"
---
![画像](https://pbs.twimg.com/media/HPDeEI4XsAA6LS8?format=jpg&name=large)

A trading strategy loses money in testing. Instead of getting deleted, it reads its own autopsy, finds the exact place it bled, rewrites its own code, and runs the test again. Nobody touched a keyboard.

**That ships today. It is not sitting in a paper waiting for 2028.**

While you read this, **$200 million** is being run on a platform where 52,000 people mostly watch the machine work. A solo quant with an AI-native workflow just landed in the **top 0.04% of WorldQuant**. **The man who runs Citadel** stood on a stage this year and said "**agentic AI does in hours what his PhDs used to do in months"**, and that he went home fairly depressed about it.

They are all circling the same thing. **A loop with six moving parts.**

Five of them are already solved.

The sixth is the one that turns a trading bot into **something that compounds.**

<video preload="none" tabindex="-1" playsinline="" aria-label="埋め込み動画" poster="https://pbs.twimg.com/amplify_video_thumb/2085424223929937921/img/_RSlamptcZPbwYP5.jpg" style="width: 100%; height: 100%; position: absolute; background-color: black; top: 0%; left: 0%; transform: rotate(0deg) scale(1.005);"><source type="video/mp4"></video>

0:02 / 0:56

## Before you read:

**\-> Bookmark this.** There is a lot below and you will not remember half of it tomorrow.

**\-> Follow me for more AI trading breakdowns:** I take this space apart piece by piece and post what holds up - [@antpalkin](https://x.com/@antpalkin)

**\-> Testing an AI trading agent takes capital you can afford to lose.** Kalshi just opened a perps exchange and **hands US users $25 free to start** -> [kalshi.com/p/fjvsjr](https://kalshi.com/p/fjvsjr)

## The loop every quant desk runs

Strip away the salaries, the Bloomberg terminals and the Greenwich office, and a hedge fund is one cycle repeating:

**\> Research** - find something worth testing

**\> Code** - turn the idea into an executable strategy

**\> Backtest** - attack it with history until it proves itself or dies

**\> Live** - push it to a broker and let it trade

**\> Post-mortem** - read what actually happened, and why

**\> Fine-tune** - fold the lesson back in so the next version is sharper

![画像](https://pbs.twimg.com/media/HPDtcEVXcAARgCN?format=jpg&name=large)

A junior quant in Greenwich runs that loop maybe once a month. **He costs $650,000 a year and roughly $87,500 per hypothesis in salary time**, and most of what he builds dies by week six. Man Group's AlphaGPT runs **hundreds of signals a week** through the same cycle. A human team does twenty in a quarter.

The loop was never the secret. **Access to it was.**

## Four jobs, one chat box

Here is the part that already collapsed.

**Horizon AI trading agent takes parts two, three and four and folds them into a sentence.** You describe a trading idea in plain English. An AI agent parses it into entry **logic, exit rules, position sizing and risk limits**, compiles it to executable code with broker bindings, and runs a **five-year backtest** in about 12 seconds. **Walk-forward, Monte Carlo across thousands of paths, Deflated Sharpe so a strategy that got lucky across 4,000 combinations cannot pass itself off as an edge.**

Then one click puts it live on a broker. **Alpaca, IBKR, Schwab, or Hyperliquid** if you want the permissionless route with no KYC and just a wallet.

**Prompt to live trade: about 90 seconds.** **Cost per hypothesis: zero with trial now, use this link -** [join.horizon.trade/explore](https://join.horizon.trade/explore)

<video preload="none" tabindex="-1" playsinline="" aria-label="埋め込み動画" poster="https://pbs.twimg.com/amplify_video_thumb/2085428076763095042/img/nDFZ3FBupuRZ_L1G.jpg" style="width: 100%; height: 100%; position: absolute; background-color: black; top: 0%; left: 0%; transform: rotate(0deg) scale(1.005);"><source type="video/mp4"></video>

![](https://pbs.twimg.com/amplify_video_thumb/2085428076763095042/img/nDFZ3FBupuRZ_L1G.jpg?name=large)

And the fifth part landed recently, which is the one I actually care about. When a backtest comes back red, the **agent shows you where the strategy bleeds, rebuilds a stronger version, and re-tests on the spot.** Test, fail, improve, repeat, until the curve turns.

That is a self-improving loop running inside a chat box. **Two years ago it was a room full of quants and a six-week cycle.**

## How to wire it up yourself

The five solved parts are worth understanding, because the sixth is built on top of them.

![画像](https://pbs.twimg.com/media/HPDuLeBWoAAX6im?format=jpg&name=large)

**\-> Research.** An agent swarm reads while you sleep. Not three filings, two hundred. Filings, transcripts, options flow, on-chain data, news at the moment of publication. The pattern that works is parallel specialist agents with no cross-talk during inference, so they do not anchor on each other. PolySwarm runs fifty distinct personas this way and deliberately hides current market prices from them, so nobody anchors on the crowd either.

**\-> Code.** This is the link that fell first and hardest. Claude Code style agentic loops write, run, debug and rewrite until the thing executes. Strategy code is small, well specified and easy to test, which makes it close to the perfect task for a coding agent.

**\-> Backtest.** The engine matters less than the discipline around it. Sealed out-of-sample windows the developer never touches. Deflated Sharpe. Walk-forward validation. A separate breaker agent whose only job is to find the conditions where the strategy dies, running it at double transaction costs and against the ten worst historical regimes for that asset class.

**\-> Live.** Broker APIs, kill switches, position state. One rule here that costs people real money when they skip it: the kill switch lives in code, never in the system prompt. A risk limit written into a prompt is a suggestion, and a sufficiently motivated reasoning chain will argue its way past it.

**\-> Post-mortem.** Every decision gets logged with the reasoning trace that produced it. A critic agent reads the journal and surfaces the pattern you keep repeating. Borrow the trick from clinical trials while you are at it: write down the expected outcome before the test runs, store it immutably, then compare. You cannot move the goalposts on a strategy that preregistered its own thesis.

## The one nobody has built

Fine-tune. The one that closes the circle.

Right now the lesson from a losing trade lands in a log file and stays there. The next strategy the agent writes does not know about it. **Every cycle starts from roughly the same place.**

Close that gap and something different appears: **a persistent world model for your own trading.** A hypothesis graph that remembers **what was tested and failed**, what was confirmed but only in bull conditions, what has never been tried. **Negative results are the most undervalued asset in quant research, and no system currently keeps them.**

![画像](https://pbs.twimg.com/media/HPDuUPhWMAAsjcp?format=jpg&name=large)

An agent that carries that graph forward does not repeat your mistakes, because it has an explicit record of every one of them, in the exact market regime where each was made.

Every component for this exists today. **Vector memory, episodic stores, LangGraph orchestration with 38 million downloads a month, agentic graphs that persist state across runs.** Nobody has assembled them end to end with real money and honest cost accounting.

**Somebody will. Probably within months, not years.**

## The moment it starts compounding

Devin Ryan put it plainly in July: **effectively everybody gets their own family office, working 24/7 for them.** Shaw Walters expects **agent-only market-making guilds by the end of this year**, swarms that beat single HFT firms by being more numerous and more specialized than any payroll allows.

Closed loop is what makes those real. Not one clever strategy. **A machine that produces a slightly better strategy every week, forever, while you decide what it is allowed to risk.**

![画像](https://pbs.twimg.com/media/HPDucQkXQAAhCHy?format=jpg&name=large)

The market has spent a century rewarding whoever could run the research cycle fastest. **Renaissance** won by running it more rigorously than anyone alive. **Jane Street pays 3,500 people** to run it and made $39.6 billion doing it.

The cycle is the whole game. **It just stopped being expensive.**

**Five parts are a chat box away. The sixth is being built right now by people who read articles like this one.**

## If you made it this far:

**\-> SAVE THIS POST** or you will be rebuilding this map from scratch in three months.

**\-> Want the first four parts done for you?** Free to try, two clicks, no code. One sentence in, tested system out -> [join.horizon.trade/cvxv666](https://join.horizon.trade/cvxv666)

**\-> $25 free to test your agents on**, US only, new Kalshi perps exchange -> [kalshi.com/p/fjvsjr](https://kalshi.com/p/fjvsjr)

**\-> remember: 99% of people will save this and never come back to it.** It costs nothing and takes two clicks. The only thing you lose by testing is the excuse.