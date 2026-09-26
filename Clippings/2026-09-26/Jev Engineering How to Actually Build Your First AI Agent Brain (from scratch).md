---
title: "Jev Engineering: How to Actually Build Your First AI Agent Brain (from scratch)"
source: "https://x.com/eng_khairallah1/status/2102767762829447540"
author:
  - "[[@eng_khairallah1]]"
published: 2026-09-18
created: 2026-09-24
description: "Open any agent you've built and look at what it spends money on.A frontier model, charged per million tokens, sitting in a loop answering qu..."
tags:
  - "clippings"
---
![画像](https://pbs.twimg.com/media/HS6F-UqXEAAJbzI?format=jpg&name=large)

Open any agent you've built and look at what it spends money on.

A frontier model, charged per million tokens, sitting in a loop answering questions like: which worker goes next? Is this urgent? Is there enough evidence to write? Should this need human approval?

None of those answers are text. They're a choice from a list, a number on a scale, a yes or no. You're renting the most expensive reasoning engine ever built to return the word "billing."

That's the gap Jev was built for, and it's why "Jev engineering" started trending the week it launched. TypeSafe AI came out of stealth on September 15, 2026 with $40M led by DCVC and a model that doesn't generate text at all. It was built by Diogo Almeida, who co-invented RLHF and InstructGPT at OpenAI.

This is the build guide. What Jev is, the exact split between what it should and shouldn't touch, a working decision router you can run today, the patterns worth stealing, and the failure modes that will bite you if nobody warns you first.

Let's build.

## What Jev Actually Is

Jev is a **System One model**. The name comes from Kahneman's split between fast, intuitive System 1 thinking and slow, deliberate System 2 reasoning. The bet TypeSafe is making: most decisions inside software are System 1 judgments, and we've all been renting System 2 to make them.

Mechanically, it works nothing like an LLM. You send it **state** (whatever context matters) plus a set of **typed questions**. It evaluates every question in a single parallel pass and returns typed answers with calibrated probabilities. No token-by-token generation. No string to parse. No chat.

The numbers TypeSafe publishes: 70 to 500 milliseconds end-to-end, $0.042 per million input tokens, and output tokens are **free**. Compare that to frontier models at seconds of latency and dollars per million tokens, with output typically charged at several times the input rate.

The simplest mental model anyone has offered: **Jev is a smart if statement.** Ordinary code branches fine on things it can compute, like if [order.total](https://order.total/) > 100. It falls apart when the condition is a judgment. Is this customer angry? Is this email about billing? Which of these twelve buttons continues checkout? Jev is the branch for those.

Hold that framing, because it makes the next part obvious.

## Step 1: Make the Split

Before any code, sort your agent's operations into three buckets. This single exercise is most of the value.

**If it creates text, it stays with the LLM.** Writing the briefing, drafting the email, generating code, explaining reasoning. Jev cannot do any of it. It's not trained to generate.

**If it picks from a list, scores a value, or answers yes/no, it goes to Jev.** Which worker acts next. How relevant this source is. Whether the draft is ready. Whether this action needs approval.

**If it's an exact rule, it belongs in code.** Stop after ten actions. Spend no more than five dollars. Count the items. Compare the dates. Anything you can compute exactly should never touch a model at all.

That third bucket matters more than people expect. The biggest savings in a well-designed cascade aren't from using a cheaper model. They're from the requests that skip the model entirely once a cheap decision routes them to plain code.

Take a job like "research three new AI-agent tools and draft tomorrow's briefing, save it for my review." Fetching sources and writing paragraphs are LLM work. The action limit and the spend cap are code. But "do we have enough sources yet," "which worker goes next," and "is this ready for review" are three Jev decisions hiding inside one sentence.

Find yours before you write a line.

## Step 2: Learn the Three Primitives

The entire API is three question types. That's not a limitation you work around; it's the design.

**Choice** picks one option from a set.

Choice( instructions="Which team should handle this", criteria={ "billing": "Payment or subscription issues", "technical": "Bugs or integration problems", "sales": "Pricing or account questions", }, )

It returns .choice, .probabilities for every option, and .confidence. It takes up to 255 options and each costs only a few tokens, so pass your full category list rather than a shortlist. Always add an explicit other option so the model can say nothing fits instead of picking the closest wrong thing.

**Score** places something on a spectrum you define.

Score( instructions="How frustrated the customer appears", criteria=\[ "Calm, just stating facts", "Frustrated but civil", "Very angry, strong language", \], )

Two to ten ordered levels, described in words. It returns .score, which can land between levels (like 1.035), plus probabilities and confidence. The level index comes from array order, so level 0 is your first entry.

**Noul** answers yes or no as a probability.

Noul(instructions="The customer is explicitly asking for a refund")

It returns .noul, a single number from 0 to 1: the probability the answer is yes. There's no separate confidence field, because the number already is the belief.

One detail that trips up everyone: **Jev never sees your question's field name.** Naming a field safe\_to\_publish contributes zero instruction. The requirement has to live in the instructions text and the criteria descriptions. Treat criteria as an extension of the instruction, and never let them contradict it.

## Step 3: Connect It

Get a key from the TypeSafe console (early access is waitlisted), or reach the model through Vercel's AI Gateway. Then:

export TYPESAFE\_API\_KEY="sk-..."

Python needs 3.10 or newer:

pip install typesafe-sdk

For JavaScript or TypeScript on Node 20+:

npm install [@typesafe](https://x.com/@typesafe)\-ai/sdk

Both SDKs read TYPESAFE\_API\_KEY from your environment and default to jev-latest. There's a single endpoint, POST <[https://api.typesafe.ai/v1/systemone](https://api.typesafe.ai/v1/systemone)\>, if you'd rather call it directly.

If you build with a coding agent, there's an official skill you can add so the agent knows the integration patterns.

## Step 4: Your First Real Call

Here's the whole thing working. One state, four questions, one round trip.

from typesafe\_sdk import Choice, Noul, Score, TypeSafeClient client = TypeSafeClient(model="jev-1.13.0") # pin the version response = client.system\_one( state={ "ticket": { "subject": "Duplicate charge", "message": "I was charged twice for order A-104. Please refund the duplicate.", }, "order": {"id": "A-104", "charges": \[ {"amount\_usd": 49, "status": "captured"}, {"amount\_usd": 49, "status": "captured"}, \]}, "refund\_policy": "Duplicate charges are eligible for a refund.", }, questions={ "department": Choice( instructions="Which team should handle this", criteria={ "billing": "Payment or subscription issues", "technical": "Bugs or integration problems", "other": "Anything that does not fit the above", }, ), "frustration": Score( instructions="How frustrated the customer appears", criteria=\["Calm, just stating facts", "Frustrated but civil", "Very angry, strong language"\], ), "refund\_requested": Noul( instructions="The customer is explicitly asking for a refund"), "policy\_supports": Noul( instructions="The stated refund policy covers this situation"), }, ) dept = response.answers\["department"\] print(dept.choice, dept.confidence) print(response.answers\["frustration"\].score) print(response.answers\["refund\_requested"\].noul)

Note the shape: answers come back under response.answers\[...\], and each answer type exposes its own field (.choice, .score, .noul). Pin the model version explicitly, because jev-latest currently resolves to jev-1.13.0 and will move when a new release ships. If you've tuned thresholds against one version, a silent bump changes every decision under you. Log the model field from the response too.

**State can be a string, a JSON object, or an array of messages.** Use an object whenever there's more than one piece of context. It's text only, so transcribe or caption anything else first.

The context limits work differently from an LLM, because state is ingested once and all questions run in parallel over it: 64k tokens for state and all questions combined, and 32k for state plus the single longest question.

## Step 5: Gate on Confidence, Not Just the Answer

This is the pattern that actually changes your architecture, and it's the one most people miss.

Jev is trained with **RLCD** (Reinforcement Learning for Calibrated Decisions), which optimizes probabilities against outcomes rather than human preference. The practical consequence: confidence is meaningful in aggregate. Higher confidence really does correlate with higher accuracy.

So stop writing one global threshold. Write one per action, scaled to what being wrong costs.

action = response.answers\["intent"\] if action.confidence < 0.5: route\_to\_human(message) # genuinely unsure, always escalate elif action.choice == "check\_balance": show\_balance(account\_id) # read-only, low bar elif action.choice == "approve\_transfer": if action.confidence > 0.85: # moves money, high bar prepare\_transfer(account\_id) # prepare, never fire else: ask\_user\_to\_confirm("Approve this transfer?") else: route\_to\_human(message)

A read-only lookup can run at modest confidence. Anything that moves money, sends a message, or can't be undone gets a high bar and a human confirmation. That asymmetry is the whole point of having a calibrated number instead of a string.

One useful diagnostic: if .probabilities comes back flat across your options, that usually means the options weren't distinguishable from the state you provided. That's a signal your criteria are wrong, not that the model is confused.

## Step 6: Ask Everything at Once

Because questions run in parallel, **a tenth question costs tokens but almost no time.** This inverts the instinct you've built on LLMs, where you make a cheap call first and follow up only if needed.

With Jev, ask everything up front and let your code decide what was relevant.

response = client.system\_one( state=ticket, questions={ "category": Choice(instructions="Broad category of this ticket", criteria={"bug\_report": "Something is broken", "billing": "Charges, invoices, refunds", "feature\_request": "Asking for new functionality", "other": "Anything else"}), # only meaningful if it IS a bug report. Ask anyway. "bug\_severity": Score(instructions="How severe is the reported issue", criteria=\["Cosmetic; no impact", "Degraded; workaround exists", "Blocking; no workaround"\]), "has\_repro": Noul(instructions="The user describes steps to reproduce"), # only meaningful if it IS billing. Ask anyway. "refund\_wanted": Noul(instructions="The user explicitly asks for a refund"), }, )

TypeSafe's own cookbook runs a 13-question briefing over a long article and reports that batching every question into one call is roughly 12x cheaper and 10x faster than asking one at a time, with identical answers.

The same trick powers the browser demo everyone screenshotted. Browser Use built an agent that turns each page observation into a numbered element table, then asks Jev for the operation and the target for click, type, and select **in the same round trip**, executing only the branch that matches the chosen operation. Two decisions, one network call. It found a Zürich to London flight on real Google Flights in 7.1 seconds for $0.0039, page loads included.

Worth being precise about that demo, since the number gets repeated carelessly: it found flight results. It did not book tickets.

## Step 7: Build the Cascade

Now assemble it into the structure that actually saves money.

def handle(message): r = client.system\_one( state=message, questions={ "intent": Choice(instructions="Primary intent of this message", criteria={"order\_status": "Asking about an existing order", "product\_question": "Asking about a product", "complaint": "Unhappy, wants resolution", "other": "Anything else"}), "complexity": Score(instructions="How complex is this to resolve", criteria=\["Simple lookup or standard procedure", "Requires judgment or multiple steps", "Unusual edge case, escalation needed"\]), }, ) intent = r.answers\["intent"\] complexity = r.answers\["complexity"\] if intent.confidence < 0.5: return route\_to\_human(message) if intent.choice == "order\_status": return lookup\_order(message) # pure code, no model at all if intent.choice == "product\_question": return handle\_with\_llm(message, PRODUCT\_SPECIALIST) if intent.choice == "complaint": if complexity.score > 1 or complexity.confidence < 0.5: return route\_to\_human(message) return handle\_with\_llm(message, COMPLAINT\_RESOLUTION) return route\_to\_human(message)

Look at what happens across those branches. One never touches a model. One loads a specialist LLM. One escalates to a person. Jev is not replacing your frontier model here. **It's deciding which requests deserve one.**

> 9月17日
> 
> 私はJevを使って1,018件のAI研究論文を分類しました。 結果：総コスト$0.08、論文あたり中央値エンドツーエンドレイテンシ256ms。 パイプラインは以下の通りでした： 1. DeepSeek V4 Flashで各論文を要約 2. タイトル + 要約 + 24の可能なトピックをJevに送信 3. Jevを使って各論文を分類 4.

The clearest public demonstration of the economics ran both models in the same pipeline and published each bill: summarizing 1,018 research papers with a generative model cost $3.99, and classifying all of them with Jev cost $0.08, at a median 256ms per paper. That ratio is the whole thesis in one line, and notice it doesn't say "replace your LLM." It says use each for what it's built for.

## The Failure Modes (Read This Before You Ship)

TypeSafe publishes a "jaggedness" page listing what the current model is bad at. It's unusually honest for a launch, and it will save you a week.

**It reads literally.** Jev answers the question you wrote, not the one you meant. Negations and implied conditions land at face value. The tell: you look at a wrong answer and catch yourself explaining what you really meant. That explanation is the missing half of your instruction.

**It is not a calculator.** It doesn't count reliably, and the error grows with the size of what's being counted. Iterate in code and ask one Noul per item instead.

**Dates are text to it, not ordered quantities.** Which came first, how far apart, whether one falls in a window, all unreliable. Extraction can be a Choice over enumerated values; ordering and arithmetic belong in code.

**Context rot is real.** Accuracy falls as state fills with material the question doesn't need. Retrieve and filter in code first, then send only the fields the question actually requires.

**It has no knowledge of the world beyond the state you hand it.** It cannot look anything up. Whatever assembles your state decides what Jev is allowed to know, which means a weak source produces a well-calibrated judgment about bad material.

**State is not treated as hostile.** Text engineered to argue for its own classification can move the answer. If user-controlled content goes into state, that's your threat model to handle, and you need to test it.

Two operational notes worth having before production: rate limits on the current version sit at 250,000 tokens per second and 1,200 requests per minute, and both SDKs retry with backoff. And log the model version, probabilities, and confidence on every decision. When three hundred refund requests land in the wrong queue at $0.0004 each, thin logging at the highest-volume layer is what makes it unreconstructable.

## The Honest Scorecard

Now the part most of the viral threads skipped, and the part that will make you sound like the adult in the room.

On TypeSafe's own four-workflow evaluation, Jev scores about 67.8%. That puts it level with mid-tier frontier models and behind the top ones, which score in the low-to-mid 70s, at roughly a two-hundredth of the cost and a fiftieth of the latency. The genuinely striking comparison is that a mainstream frontier model scores identically at hundreds of times the cost per case.

> 9月18日
> 
> これは本当に狂ってる。このツールは@typesafeai JevモデルをClaudeのプラグインとして使って、すべての不要なツールコールをレビューするんだ。実行に1秒しかかからない！ つまり、本当に、1秒で私のClaudeセッションのトークンをほぼ100万から... 8万6000に減らしたんだ！

> 9月18日
> 
> @typesafeai Jevのための完璧なユースケースを見つけました： インスタント・コンパクション 2026年になっても、なぜコンパクションはまだ要約プロンプトのままなのか？ Jevはすべてのツールコールをスコアリングして、関連性のないものをドロップすることで、それをインスタントにできます

Four caveats you should carry:

**That column isn't accuracy.** There's no ground truth. TypeSafe builds consensus labels by averaging two frontier models and scores everyone against those, which measures agreement with those two models, and TypeSafe says so itself.

**It's self-run.** TypeSafe designed the workflows, built the harness, and ran it. No independent reproduction exists yet. Evaluate on your own traffic before you trust anyone's benchmark, including theirs.

**"Cannot hallucinate" is narrower than it sounds.** Jev cannot return a value outside your schema. It can absolutely return the wrong valid one. The 0% structured-output error rate is asserted by construction, not measured empirically.

**The price may move.** Nobody outside the company can tell whether current pricing is subsidized.

None of that makes Jev uninteresting. It makes it a tool with a shape, which is the only kind worth learning.

## The Real Alpha

The headline demos are the 7-second flight lookup and the $0.08 classification run. Those aren't the insight.

The insight is how many expensive model calls inside your agents never needed generation in the first place. Every route, every score, every approval check, every "is this ready," all of it has been paying frontier-model prices for an answer that was always going to be one word from a list you already wrote.

So the split is the whole discipline:

**Let the LLM research, plan, and write. Let Jev route, score, approve, and escalate. Let code execute the decision and own anything irreversible.**

That last clause matters as much as the other two. A calibrated confidence score is a reason to route more aggressively, never a reason to let a model fire an action that can't be undone. Prepare, then approve. Always.

**Most builders will keep spending frontier tokens on every yes, no, route, and score, because it's what they already know how to do.**

The few who separate thinking from deciding will ship agents that run faster, cost a fraction as much, and fail in ways they can actually inspect.

Start with one repeated decision. Measure it against your own traffic. Then replace the next one.