---
title: "Everything Fable 5."
source: "https://x.com/mikenevermiss/status/2073278341377912944"
author:
  - "[[@mikenevermiss]]"
published: 2026-07-04
created: 2026-08-01
description: "learn how Claude Fable 5 works, how to prompt it well, and how to get real work out of it today. understand its effort levels, know when it ..."
tags:
  - "clippings"
---
![画像](https://pbs.twimg.com/media/HMWkteLWUAA3zIx?format=jpg&name=large)

learn how Claude Fable 5 works, how to prompt it well, and how to get real work out of it today. understand its effort levels, know when it will reroute your request, and leave with eight copy-paste-ready prompts you can use immediately.

\---

- **What You'll Learn**

\- what "**Mythos-class**" means and how Fable 5 differs from every Claude model before it

\- what Fable 5 is genuinely better at, and where it still underperforms for your use case

\- how its safety classifiers work, what triggers them, and what to do when one fires on you

\- how to access Fable 5 today on [claude.ai](https://claude.ai/), Claude Code, and Claude Cowork

\- how to use effort levels to control how deeply the model works, without touching the API

\- eight prompts you can paste straight into [claude.ai](https://claude.ai/), each built for a specific type of task

\- the five most common mistakes that waste Fable 5's capability and how to avoid them

\- when you should skip Fable 5 entirely and use Sonnet 5 or Haiku 4.5 instead

**What Fable 5 Actually Is**

Claude Fable 5 is Anthropic's most capable model available to the public. it sits above the Opus class, in a new tier Anthropic calls "Mythos-class," meaning models whose capabilities are powerful enough to require a new category of safety measures before general release.

think of Mythos-class as the tier where the model is capable enough to complete weeks of real work autonomously: not faster answers, but entirely different kinds of tasks that weren't possible to hand off before.

Fable 5 launched June 9, 2026, alongside Claude Mythos 5. Mythos 5 is the same underlying model with fewer safety restrictions, currently limited to vetted partners through Anthropic's Project Glasswing. Fable 5 is the version built for general use, with extra safety layers called classifiers (separate AI systems that watch incoming requests and filter out high-risk ones) baked in.

there was a gap: Fable 5 was suspended from June 12 to July 1 following a US export-control order tied to a third-party jailbreak report. Anthropic disputed the severity of the finding, updated its classifiers, and access was restored on July 1 globally.

- **What It's Genuinely Good At**

the biggest performance gains show up on long, multi-step work, not quick one-offs.

Stripe compressed months of engineering into days using Fable 5. in a 50-million-line Ruby codebase, it performed a full migration in a day that would have taken a team more than two months by hand. that is not the kind of thing you get from a better chatbot. it is the kind of thing you get from a model that can plan, execute, check its own work, and keep going across an extended task without losing the thread.

in knowledge work, Fable 5 handles document-heavy analysis, chart and table interpretation, and multi-source research with strong accuracy. it scored highest of any model tested on Hebbia's Finance Benchmark for senior-level reasoning. IMC reported it aced their trading-analysis evaluations nearly across the board.

for vision, it is state of the art. it can rebuild a web app's source code from screenshots alone, extract precise data from dense scientific figures, and handle blurry or flipped images without needing you to prepare the file first.

the memory and context story is worth noting: Fable 5 has a 1-million-token context window by default, meaning it can hold and reason across the equivalent of several long books in a single session. it can also write notes to itself mid-task and use them to improve output over the course of a long run.

the biggest gains over previous Claude models show up when you give it hard, long, ambiguous problems. testing it only on simple tasks, as the official docs note, tends to undersell its range.

- **How the Safety System Works**

Fable 5 has built-in classifiers that monitor every request. a classifier is a separate AI system that runs in parallel with the main model, watching what you ask and deciding whether the request falls into a category that requires extra caution.

when a classifier flags a request, your message is automatically handled by Claude Opus 4.8 instead of Fable 5. you will be told when this happens. the rerouted request is not billed at Fable's rate. you still get a response, just from Opus.

the three domains that trigger reroutes are cybersecurity (anything related to finding or exploiting software vulnerabilities), biology and chemistry (lab methods, molecular mechanisms, gene-related content), and distillation (large-scale attempts to extract the model's capabilities to train other models). the classifiers are deliberately set wide, meaning some benign requests will get caught. anthropic says this happens in less than 5% of sessions on average, but the range in biology and chemistry can be broader since those classifiers are tuned conservatively.

if you get rerouted, try two things before giving up. first, add more context about why you are asking. a researcher asking about a protein mechanism for a published paper is different from a vague query with no framing. second, rephrase the request to be more specific about the benign purpose. the classifiers look at intent signals. more context changes the signal.

one thing that reliably triggers classifiers: asking Fable 5 to explain its own internal reasoning in the visible reply. if your prompt tells the model to "show your thinking" or "narrate your reasoning" as part of the output, that can trip the reasoning-extraction classifier and cause a fallback to Opus. if you need to see how it reasoned, use Claude Code's structured thinking output instead.

- How to Access Fable 5 Today

on [claude.ai](https://claude.ai/): select Claude Fable 5 from the model dropdown when starting a new conversation. you need a paid plan: Pro, Max, Team, or Enterprise.

on Claude Code: open Claude Code, select Fable 5 from the model selector. it is available in the desktop app and the CLI.

on Claude Cowork: available in the model selector within the desktop app on paid plans.

via the API: use the model string \`claude-fable-5\`. pricing is $10 per million input tokens and $50 per million output tokens, with a 90% discount on cached context.

\---

\> pricing and limits notice: the access terms below are current as of July 4, 2026. verify at [claude.com/pricing](https://claude.com/pricing) and [support.claude.com](https://support.claude.com/) before relying on them. these terms have changed three times since launch.

\---

since the July 1 relaunch, Pro, Max, Team, and select Enterprise plans include Fable 5 for up to 50% of weekly usage limits through July 7, 2026. after July 7, using Fable 5 requires usage credits at the standard API rate. anthropic has said it intends to restore fuller plan access when capacity allows but has not given a date. confirm this before publishing or acting on it.

Effort Levels: The Dial That Controls How Hard It Works

effort is the main control for balancing intelligence, speed, and cost. think of it as a dial with four settings: low, medium, high, and xhigh.

"effort" is a confirmed API and Claude Code parameter. in Claude Code, you can set it directly. in [claude.ai](https://claude.ai/) chat, there is no labeled toggle for effort levels, but you can achieve the same effect through plain language in your prompt.

low and medium effort: faster, cheaper, still capable. a Fable 5 response at medium effort often beats prior Claude models at maximum effort. use these for routine tasks, summaries, quick drafts, or anything where speed matters more than depth.

high effort: the recommended default for most real work. the model gathers more context, checks its own output, and produces more rigorous results. this is where you will see it plan before acting and verify before reporting.

xhigh effort: for the hardest, most capability-sensitive tasks. the model will deliberate more, run longer, and check its work more thoroughly. one of Knotting's early testers noted that "at the highest effort, Claude Fable 5 reflects on and validates its own work. for us, that's what makes highly autonomous operations possible." xhigh is slower and more expensive. save it for the work that actually needs it.

plain-language workaround in [claude.ai](https://claude.ai/): to get lower effort behavior, open with "give me a quick pass on this" or "I need a fast first draft, not a thorough analysis." to get higher effort behavior, open with "this is important, I want your most thorough work" or "take your time, check your reasoning carefully before responding." the model responds to explicit intent framing.

Prompts That Unlock Fable 5:

each prompt below follows the pattern that gets the best results: state the goal and success criteria, give the reason behind the request, and tell it when to stop versus keep going.

\---

1\. long document analysis

use this when: you have a dense report, contract, or research paper and need more than a summary.

\`\`\`

I'm preparing for a client presentation on \[topic\]. the attached document is the primary source. my goal is to identify the three most consequential findings and any points where the evidence is weak or contradictory. do not summarize the whole document. go section by section, flag what matters most, and tell me when something needs more verification before I cite it. stop and check with me before making any claims you're not confident about.

\`\`\`

\---

2\. multi-source research brief

use this when: you need to synthesize across several inputs, not just one.

\`\`\`

I'm building a research brief on \[topic\] for \[audience\]. I've attached \[X sources\]. my goal is a structured brief that identifies where the sources agree, where they conflict, and what questions remain unanswered. do not write a summary of each source separately. synthesize across all of them. flag any gaps that would materially affect the conclusion. when you're done, tell me what additional sources would strengthen the brief.

\`\`\`

\---

3\. long-form content draft

use this when: you need a full piece written in a specific voice, not a skeleton or outline.

\`\`\`

I'm writing a \[type of content\] for \[audience\]. my goal is a complete first draft, not an outline. the piece should \[describe purpose\]. here is an example of the voice and format I want: \[paste 2-3 paragraphs of your own writing\]. match that voice exactly. if you're uncertain whether a section serves the purpose, flag it rather than cut it. deliver the full draft, then list any sections you were uncertain about at the end.

\`\`\`

\---

4\. project planning with real constraints

use this when: you have a goal but need a realistic plan with tradeoffs identified.

\`\`\`

I'm planning \[project\] with a team of \[X\] people and a deadline of \[date\]. my goal is a realistic phased plan, not an ideal one. identify where the schedule is tight, what dependencies could cause delays, and what I should cut first if time runs short. do not give me a generic project template. use the constraints I've given you. at the end, flag the two or three decisions that will most affect whether this succeeds.

\`\`\`

\---

5\. spreadsheet or data analysis

use this when: you have data and need interpretation, not just calculation.

\`\`\`

I'm analyzing \[type of data\] to \[business goal\]. I've attached \[file\]. my goal is to identify patterns that would not be obvious to someone looking at the raw numbers. start by describing what the data actually contains and any quality issues you notice. then give me the two or three findings most relevant to \[goal\]. flag anything I should not conclude from this data alone. stop and check with me if you find something that contradicts what I told you to expect.

\`\`\`

\---

6\. extended autonomous task (Claude Code)

use this when: you are running Fable 5 on a multi-step task inside Claude Code and will not be watching in real time.

\`\`\`

I'm working on \[the larger task\] for \[who it's for\]. they need \[what the output enables\]. proceed end to end without checking in unless you hit a destructive or irreversible action, a real scope change, or something only I can provide. for those cases, pause and tell me exactly what you need. before reporting any progress, verify each claim against actual results. if something failed, say so plainly. do not end your turn with a plan or a list of next steps you haven't done yet. finish the work.

\`\`\`

\---

7\. feedback and editing

use this when: you want surgical edits, not a full rewrite.

\`\`\`

I have a draft of \[type of content\]. I want specific feedback, not a rewrite. for each issue you find, quote the sentence or paragraph, identify what's wrong, and suggest the precise fix. do not improve things that are working. do not give me general notes like "this section could be stronger." if a section is weak, tell me exactly why and exactly what to change. flag anything where you're uncertain whether it's a problem or a stylistic choice.

\`\`\`

\---

8\. decision analysis

use this when: you're weighing a real choice and need structured reasoning, not validation.

\`\`\`

I'm deciding \[decision\]. the relevant context is \[situation\]. I'm considering \[option A\] versus \[option B\]. I'm asking for analysis, not agreement. identify the strongest argument for each option, the biggest risk I'm probably underweighting, and what additional information would most change the decision. do not tell me what you think I want to hear. if the right answer is "you don't have enough information yet," say that. end with one clear recommendation and the one thing I should verify before acting on it.

\`\`\`

Common Mistakes:

using it for small tasks; Fable 5 is built for long, complex work. using it to summarize a short email or answer a quick question is like using a freight elevator to carry a single bag. sonnet 5 or haiku 4.5 will be faster and cheaper for routine tasks.

expecting instant replies; at high and xhigh effort, Fable 5 can take minutes on a hard task. it is planning, executing, and checking its own work. if you start a serious task and close the window after thirty seconds, you have not given it enough time to do what it was built to do.

treating a classifier decline as a final answer; if Fable reroutes your request to Opus, that is the classifier being cautious, not a permanent refusal. try adding context about why you're asking. rephrase to be more specific. restate the benign purpose. most legitimate requests that get caught are caught because they lack framing, not because the task is actually off-limits.

never telling it when to stop; without explicit stopping conditions in your prompt, Fable 5 will keep going. it may draft follow-ups you didn't ask for, refactor code beyond the bug you reported, or pad a deliverable with content the task didn't require. tell it what done looks like.

asking it to explain its internal reasoning in the response; prompts like "show your thinking" or "narrate your reasoning step by step" can trigger the reasoning-extraction classifier and cause your request to fall back to Opus. if you need to see how it reasoned, use Claude Code's structured thinking output, not a plain-text instruction to narrate reasoning.

**Who Should Skip It:**

Fable 5 is not the right tool for every job.

if you need quick answers, a single sentence output, or a fast creative riff, sonnet 5 is faster and significantly cheaper. if you're on a tight budget and handling high volumes of routine tasks, haiku 4.5 is designed exactly for that.

if latency matters for your use case because you're building a real-time tool or a high-volume pipeline, Fable's longer response times at higher effort are a real cost. sonnet 5 and haiku 4.5 were built with speed as a design priority in ways Fable was not.

if your work regularly falls in cybersecurity, biology, or chemistry domains, the classifier rerouting to Opus 4.8 will be a constant friction point until Anthropic refines those classifiers. depending on how central those domains are to your work, Opus 4.8 may be the more reliable daily driver right now.

Further Reading:

\- what Fable 5 is: [https://www.anthropic.com/claude/fable](https://www.anthropic.com/claude/fable)

\- launch announcement: [https://www.anthropic.com/news/claude-fable-5-mythos-5](https://www.anthropic.com/news/claude-fable-5-mythos-5)

\- the suspension and relaunch: [https://www.anthropic.com/news/fable-mythos-access](https://www.anthropic.com/news/fable-mythos-access) and [https://www.anthropic.com/news/redeploying-fable-5](https://www.anthropic.com/news/redeploying-fable-5)

\- official prompting guide: [https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5)

\- model introduction (API, pricing, model ID): [https://platform.claude.com/docs/en/about-claude/models/introducing-claude-fable-5-and-claude-mythos-5](https://platform.claude.com/docs/en/about-claude/models/introducing-claude-fable-5-and-claude-mythos-5)

\- current access and limits: [https://support.claude.com/en/articles/15424964-claude-fable-5-promotional-access](https://support.claude.com/en/articles/15424964-claude-fable-5-promotional-access)

\- why claude switched models mid-conversation: [https://support.claude.com/en/articles/15363606-why-claude-switched-models-in-your-conversation-with-fable-5](https://support.claude.com/en/articles/15363606-why-claude-switched-models-in-your-conversation-with-fable-5)

\- current pricing: [https://claude.com/pricing](https://claude.com/pricing)

\---

your next action:

open [claude.ai](https://claude.ai/), select Fable 5, and paste prompt 8 (the decision analysis template) into a real decision you're currently sitting on. that is the fastest way to see what it actually does versus what any article can describe.

questions about any of the prompts or how to adapt them to your use case, drop them below in the comment.