---
title: "Loops Engineering: Claude, GPT, and What Actually Works"
source: "https://x.com/eng_khairallah1/status/2082393505964470479"
author:
  - "[[@eng_khairallah1]]"
published: 2026-07-29
created: 2026-07-31
description: "Most people hear \"AI agent loops\" and assume it's something complicated that requires a research background.Save this :)A small group of peo..."
tags:
  - "clippings"
---
![画像](https://pbs.twimg.com/media/HOVLsIRXAAA1819?format=jpg&name=large)

Most people hear "AI agent loops" and assume it's something complicated that requires a research background.

Save this :)

A small group of people understood the idea in about five minutes and started using it to get real work done while they slept.

The difference between those two groups is not intelligence. It's that nobody ever explained loops to the second group in plain language, so it stayed mysterious. Let me fix that.

By mid-2026, loops became the dominant way serious people work with AI. The core idea replaced the old rhythm of prompt, wait, review, prompt again with systems that run themselves. And both major ecosystems, Claude and GPT, now support them. This is the plain-English explanation of what a loop actually is, how the two big platforms do it, and, most importantly, what actually works versus what just sounds impressive.

## What a Loop Actually Is

Start with the simplest possible picture.

For three years, using AI meant you were the loop. You typed a prompt, read the answer, decided what to do next, and typed again. The AI did one step; you did all the deciding in between. You were the part that kept it going.

A loop takes you out of the middle. Instead of you deciding what to do after each answer, the system decides. It works, looks at what happened, and figures out the next move on its own, over and over, until the job is done. That's the whole idea. A loop is what runs when the AI, not you, is the thing driving each next step.

The rhythm inside every loop is the same three beats: reason, act, observe. The agent reasons about what to do, acts by using a tool, and observes the result. Then it reasons again with that new information and goes around again. Think, do, look, repeat. That's a loop.

## Loop Versus Chain: The Distinction That Matters

People mix these up constantly, and the difference is the whole point.

A chain is a fixed recipe. Step one, then step two, then step three, done. You define every step in advance, and it runs them in order. Chains are great when you already know exactly what needs to happen. Summarize this, then translate it, then format it. Fixed steps, predictable path.

A loop is different because it doesn't know the path in advance. It tries something, sees the result, and decides the next step based on what actually happened. This is what you need when the steps depend on each other. You can't write a fixed recipe for "fix this bug," because step three depends entirely on what step two revealed. A chain can't do that. A loop can, because it observes and adapts.

Here's the simple test. If you can write down every step ahead of time, use a chain. If the next step depends on what the last step returns, you need a loop. Real, messy, multi-step work almost always needs a loop.

A quick way to feel the difference: imagine giving instructions to a new assistant. If you can hand them a numbered checklist and walk away confident it'll work, that's a chain, the path is knowable in advance. If instead you'd have to say "try this, and depending on what you find, do one of these next things," that's a loop, because the right next move genuinely can't be known until you see the result of the last one. Most valuable work is the second kind. That's why loops took over: the interesting problems are exactly the ones where you can't write the full recipe up front.

## Why Loops Became the Big Deal in 2026

Loops aren't new as an idea. The reason-act-observe pattern goes back to research from 2022, and self-critiquing agents were being studied in 2023. So why did loops suddenly become the center of gravity in 2026?

Because the models finally got reliable enough. Early models running in a loop would drift, get confused, or make things worse with each iteration. You couldn't trust them to run unsupervised, so you stayed in the middle. By 2026, the frontier models became reliable enough at long, multi-step work that you could actually let go. The bottleneck moved. The slow part was no longer the model. It was you, the human, sitting in the middle typing.

So the skill changed. It stopped being about writing the perfect prompt and became about designing the loop that prompts the AI for you. That's the shift that got a name, loop engineering, and spread across the whole field in a matter of weeks.

## How Claude Does Loops

In the Claude ecosystem, loops center on Claude Code, the agentic coding tool, and they're built from a few pieces that fit together.

There's a built-in loop capability for running a repeating cycle against a goal, so you can tell it to keep working until a check passes rather than issuing one command at a time. There's scheduling, so a loop can run on a timer without you starting it, which is how a loop runs overnight. There are hooks, which automatically fire a rule at specific moments, like running your tests after every change, so verification can't be skipped. And there are dynamic workflows with sub-agents, which let you split a big job across many parallel workers coordinated by a main agent.

Underneath, loops reach tools through the Model Context Protocol, so a loop can touch your real systems, and they can use skills, which are saved procedures the agent follows, and worktrees, which keep parallel lines of work from colliding. The overall philosophy in the Claude world leans toward long-running, self-verifying loops that check their own work with real tests and keep going until the job is genuinely done.

## How GPT Does Loops

In the OpenAI ecosystem, the equivalent centers on Codex, and the pieces are similar in spirit with their own names and design.

There's an Automations capability with configurable schedules, so loops can run on a timer, the same "runs while you sleep" idea. There's sub-agent spawning, so a job can be split across parallel workers. And like Claude, it supports skills for saved procedures and integrates with the Model Context Protocol for tool access, so loops can reach external systems.

The important thing to understand is that both ecosystems converged on the same fundamental architecture. A loop that drives an agent toward a goal, tools it can use, sub-agents for parallel work, scheduling to run unattended, and saved skills to reuse procedures. The names differ and the details differ, but the shape is the same, because the shape is dictated by what the problem actually requires.

## The Insight That Should Change How You Think

Here's the thing almost nobody tells you, and it's more important than any platform comparison.

The valuable, durable part of a loop is not the model at the center. It's everything around it: the loop logic, the tools, the checks, the memory, the guardrails. And that outer part is largely portable. Switching from Claude to GPT, or vice versa, mostly swaps the central brain. The loop you designed, the verifier you built, the tools you wired up, the definition of done you wrote, all of that carries over.

This means the argument about which model is best is much less important than it seems. Both ecosystems have highly capable models. What determines whether your loop works is the system you build around whichever model you choose. Invest your effort there, in the reusable outer layer, not in endlessly re-litigating which brain is a few points better this month.

## What Actually Works

Now the part that matters most. Across both platforms, certain things separate loops that work from loops that waste your money. These are platform-independent. They're just true.

**A definition of done a machine can check.** The number one failure is a vague goal. "Make it good" gives the loop no way to know when to stop. "All tests pass" or "the output matches this format" does. Write a checkable definition of done first, every single time. This one habit prevents most loop failures.

**A grounded verifier, not self-grading.** This is the big one. A model checking its own work almost always approves itself. An agent grading its own homework gives itself an A. So the check that decides success must come from outside the model's own opinion whenever possible: a test suite, a linter, a build command, an external validator. When the verifier is a real command, there's nothing to flatter. This single principle is the difference between a loop that improves output and a loop that produces confident garbage.

**Termination logic in layers.** A loop with no exit is the most expensive mistake in the field. You need several exits stacked: the success check passing, a hard cap on iterations, a budget or time limit, and no-progress detection that stops the loop when nothing is changing. Any one alone isn't enough. Together they make a loop safe to walk away from.

**Cost discipline.** Loops make ten to a hundred times more model calls than a single prompt. The naive approach gets expensive fast. What works is routing each step to the cheapest capable model, a small fast one for simple sorting, a bigger one only for the hard final review, and reusing the repeated parts of your prompts so you're not paying full freight every iteration. Done well, this cuts loop costs dramatically without hurting quality.

**A human checkpoint before anything irreversible.** The loop can research, draft, code, and verify on its own all day. But sending a message, deploying, pushing to a shared branch, moving money, or deleting anything should pause for your approval. This isn't a lack of ambition. It's what makes running loops unattended actually safe.

## What Sounds Impressive But Often Doesn't Work

Just as important: the things that look good in a demo and fail in reality.

**Loops that grade themselves with a vague rubric.** They feel like they're working because they always pass their own check. Then you look at the output and it's wrong. Impressive-looking, useless. Ground the check.

**Endless self-improvement with no guardrails.** An agent left to critique and revise itself hundreds of times without external grounding doesn't get better. It drifts. Research in 2026 found measurable degradation after surprisingly few iterations, with agents diverging from their original intent while still sounding fluent. One builder watched his agent start inventing statistics and then citing its own made-up numbers as fact. Self-improvement only works with grounded checks, memory hygiene, and hard limits.

**Maximum autonomy on everything.** Letting a loop do irreversible things unsupervised because "it's usually right" is how you get a bad deploy you can't undo. The pros gate the irreversible and free the reversible.

**Chasing the model instead of building the system.** Switching brains for a few benchmark points while ignoring a broken verifier and no termination logic. The model was rarely the problem.

## Your First Loop, Step by Step

Let me take loops out of the abstract and walk you through building one, on whichever platform you have open. The steps are the same on Claude and GPT.

Pick a task you do repeatedly where you can clearly say what a good result looks like. Fixing failing tests is the classic starting point, because the check is objective and already exists, but it can be anything with a checkable output: cleaning a messy data file into a valid format, drafting something against a strict rubric, or completing every item on a structured checklist.

First, write down what "done" means as something a machine can verify. Not "make it good." Something like "the file passes validation" or "all tests are green." This is the single most important line you'll write, because it's what tells the loop when to stop.

Second, identify your verifier. For code, it's your test suite or linter. For a data file, it's a validation check. For writing, it's a rubric checked by a separate reviewer step. Whatever it is, make sure it lives outside the agent's own opinion, so the loop can't just declare itself finished.

Third, set your exits. A hard cap on iterations, a budget or time limit, and a rule to stop if nothing's changing. Write these before you run anything.

Fourth, run it and watch. Don't walk away on the first run. Look for the ways it fails: looping forever, passing its own weak check, wandering off the goal. Each failure tells you how to tighten the verifier or the instructions.

Fifth, once you trust it, let it run unattended, and if it's the kind of task that recurs, schedule it. That's the moment it stops being something you operate and becomes something that just happens.

That's a loop. Not a research project. A goal, a grounded check, some exits, and the discipline to watch before you trust. You can build your first one in an afternoon.

## When You Should Not Use a Loop

Loops are powerful, which makes it tempting to reach for them everywhere. Don't. Knowing when a loop is the wrong tool is part of using them well.

If the task is a single step, you don't need a loop, you need a prompt. Asking a question, summarizing one document, writing one email: these are done in one shot, and wrapping them in a loop just adds cost and complexity for nothing.

If every step is known in advance and never depends on the last step's result, you want a chain, not a loop. Fixed sequences run more predictably and cheaply as chains. The loop's whole advantage, adapting based on what happened, is wasted when nothing needs adapting.

If you can't define what "done" looks like in checkable terms, you're not ready to loop yet. A loop with no clear verifier either stops too early or runs forever. Get clear on the goal first; the loop comes after.

And if the task is high-stakes and irreversible with no room for a human checkpoint, think hard before automating it in a loop at all. Loops shine on work that's repetitive and verifiable. They're a poor fit for one-off decisions that can't be undone and can't be checked.

The skill isn't using loops for everything. It's recognizing the tasks that are genuinely loop-shaped: repetitive, multi-step, adaptive, and checkable. Those are the ones where a loop turns hours of your time into work that runs itself.

## Where Loops Came From, in Plain Terms

You don't need the academic history to build loops, but a little context helps you understand why the pieces are shaped the way they are, and it makes you sound like you actually know the field rather than just the buzzwords.

The core idea, having a model reason, act with a tool, then observe the result and reason again, was studied back in 2022. That's the reason-act-observe rhythm at the heart of every loop today. The idea of an agent critiquing and improving its own work came shortly after, in 2023, with research on agents that reflect on failures and try again with the lesson in hand. So the fundamental patterns are several years old.

What's new in 2026 isn't the idea. It's that the models finally got good enough to make the idea practical. For years, an agent running in a loop would drift or degrade with each pass, so you couldn't trust it to run on its own. Once the frontier models became reliable at long, multi-step work, you could finally let the loop run without babysitting it, and the whole approach went from research curiosity to the dominant way people work. The concept got a name, loop engineering, and spread across the field in weeks. Understanding this arc tells you something useful: the loop patterns are stable and worth learning deeply, because they're not a fad. They're a decade-old idea that just became usable.

## The Questions Everyone Asks About Loops

A few questions come up constantly from people new to loops. Quick, honest answers.

Do I need to know how to code to build loops? For the most powerful coding loops, some comfort with code helps a lot. But the core thinking, define done, build a check, set exits, is not about code, and plenty of useful loops for research, writing, and data work can be built with far less technical depth than people assume.

Which platform should I learn on? The one you already use. Both major ecosystems support loops with the same fundamental architecture, and the skill transfers. Don't stall on the platform choice; the reusable skill is the loop design, not the specific commands.

Won't better models make loop engineering unnecessary? The opposite. Better models make loops more powerful, which makes the skill of designing them more valuable, not less. The model handles the doing. Deciding what to do, what "done" means, and how to verify it stays with you, and gets more leveraged as the model gets stronger.

Is this just hype? The terminology has hype around it, sure. But the underlying shift, from you typing every step to systems that run themselves, is real and already changing how serious people work. Strip away the buzzwords and a loop is just a sensible way to get repetitive, verifiable, multi-step work done without sitting in the middle of it. That's not hype. That's leverage.

## The Honest Truth About Loops

A loop will not fix a task you can't clearly describe.

Whether you're on Claude or GPT, the entire practice reduces to two hard things: describing your work precisely enough that a system can drive it, and building a check honest enough to catch failure. The platform features are the easy part. The clarity and the grounded verifier are the real work, and no model does that part for you.

That's also the good news. As both ecosystems' models keep getting better, the skill that stays scarce is designing the loop: setting the bar, encoding your judgment as a check, and knowing what "correct" looks like. That skill is portable across every platform and durable across every model release. It doesn't get automated away. It gets more valuable.

Most people still think loops are complicated and stay stuck typing prompts one at a time.

You now understand them better than nearly everyone in your feed, on either platform.

The only question left is whether you go build one, on whichever model you already have open.

**If you found this useful, follow me** [@eng\_khairallah1](https://x.com/@eng_khairallah1) **for more AI content like this. I post breakdowns, courses, and tools every week.**

**hope this was useful for you, Khairallah** **❤️**