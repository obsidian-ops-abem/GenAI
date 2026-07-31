---
title: "Context Engineering: Claude Codeの文脈設計（出典）"
tags: [raw-source]
source: https://x.com/hanakoxbt/status/2081800965502513286
author: Hanako (@hanakoxbt)
published: 2026-07-27
created: 2026-07-30
---

# 出典メタデータ

- URL: https://x.com/hanakoxbt/status/2081800965502513286
- 著者: Hanako（@hanakoxbt）
- 公開: 2026年7月27日（JST 翌日早朝に投稿、UTC 17:56:42）
- 形態: Xポスト（長文）。同著者の外部記事への導線（t.me Telegram）も含む
- インプレッション等: 自己申告ベース（外部検証なし）

要約は [[08_Context Engineering Claude Codeの文脈設計]] を参照。

---

# 本文（完全転載）

Everyone optimizes the forty-five tokens they type.

Nobody has ever opened the seven thousand eight hundred that load before them.

That is the actual ratio.

Before you type a single character, Claude Code has already filled its window.

Your request is barely half a percent of what the model is reading.

The rest reloads on every single request, for the life of the project.

A bad prompt costs you one answer.

A bad line of context costs you a fraction of every answer Claude will ever give you here.

This is the discipline that fixes that.

Not writing better instructions.

Engineering the window they live in.

---

## What is already in the window before you speak

Anthropic publishes a session simulation with representative token counts.

The shape of it is the whole argument.

The system prompt lands first.

Auto memory follows, capped at the first 200 lines or 25KB, whichever comes first.

Then environment info, MCP tool names, one-line skill descriptions, your global CLAUDE.md, and the project CLAUDE.md.

That is roughly 7,850 tokens before your prompt exists.

Then you type your actual request.

Forty-five tokens.

Prompt engineering optimizes those forty-five.

Context engineering optimizes the 7,850.

And unlike your prompt, they reload every time.

Those numbers are representative. Yours are not.

Claude Code ships a command that prints your real window.

Run `/context` and you get a colored grid plus a per-category breakdown.

System prompt, system tools, MCP tools, custom agents, memory files, messages, free space, autocompact buffer.

It tells you which files loaded and which tool calls ate the most tokens.

Two habits separate people who engineer context from people who guess at it.

Run `/context` at the start of a session to see your fixed overhead.

If memory files are taking meaningful space, your CLAUDE.md is too long, and you just diagnosed it in one command.

Then run it again mid-session, rather than waiting for Claude to warn you.

Pair it with `/memory` to open the exact files that loaded.

And `/usage` to break recent spend down by skills, subagents, plugins, and individual MCP servers.

---

## The four ways it fails

"Too much context" is not a diagnosis.

Drew Breunig catalogued four distinct failure modes.

Naming them turns a vague sense that Claude is being dumb into a specific thing you can go fix.

**Poisoning.** A hallucination makes it into context and everything downstream builds on it. The model is not wrong once. It is wrong consistently, from a bad premise it now treats as given.

**Distraction.** The accumulated context grows so large it overwhelms what the model knows from training. It stops reasoning and starts pattern-matching against the pile you handed it.

**Confusion.** Superfluous context influences the response. This is the classic symptom of loading tools and files that have nothing to do with the task. The model reaches for something that was in the window rather than something that was right.

**Clash.** Parts of the context disagree with each other. Three of those arrive on their own. The fourth you author yourself. Anthropic found it in their own transcripts. A single request arriving with one source saying to document as appropriate, another saying never add comments, and a user asking for something else again. Claude generally reads intent and lands on the right answer. But it has to think harder to reconcile the contradiction first. You pay that tax on every request, invisibly, because the output still looks fine. That is what makes clash the expensive one. Poisoning is loud. Clash is silent and permanent, sitting in a file you wrote six months ago and have not read since.

---

## Delete first, measure second

The headline number is the argument.

Anthropic removed over 80% of Claude Code's system prompt for the Claude 5 generation.

Their coding evaluations showed no measurable loss.

Not a rewrite. A deletion, of guidance that felt load-bearing right up until it was gone.

Invert your default.

Most people treat context as innocent until proven harmful. It is one line, what is the harm.

Treat it as guilty until proven useful.

The method is unglamorous and it works.

1. Build the smallest eval harness you can stand. Ten prompts you care about, a rubric for what good looks like. Without it you are guessing.
2. Cut a whole block, never a single line. One sentence at a time sits inside the noise floor and tells you nothing.
3. Run the harness. If nothing moves, the cut stays.
4. Restore only what actually broke. You will be surprised how rarely you restore.

---

## Trade rules for judgement

When Claude Code first shipped, the priority was avoiding worst cases.

That justified guidance stated far more strongly than it was actually true.

The old system prompt told Claude to default to no comments, never write multi-paragraph docstrings, never create planning documents unless asked. Absolute, unconditional, easy to follow. And wrong for a real slice of prompts, since some users want documentation and some genuinely complex code needs a multi-line block.

With older models the comments Claude wrote were incorrect often enough that the tradeoff was worth accepting. Newer models have the judgement to make that call themselves, so the rule stopped paying for itself.

What replaced it is a principle rather than a prohibition.

Write code that reads like the code around it, matching its comment density, its naming, its idiom.

The rule gives a fixed answer that is wrong for some prompts. The principle gives Claude a way to find the right answer by reading the repo. So it adapts to a codebase the rule-writer never saw.

Apply that to every absolute you have written.

"Always X" becomes "X is usually right here because of this reason."

"Never Y" becomes "prefer Z, Y is a known gotcha in this repo."

Keep as hard rules only what Claude genuinely cannot infer. Destructive operations, security boundaries, compliance, and the gotchas that are invisible from the code itself.

That is the shape every line should take. Not what to output. What to look at.

---

## A tree, not a scroll

The myth that inflates every CLAUDE.md is the belief that it must hold every practice you might ever need. Because otherwise Claude will not find it.

That was once close to true. It is not now.

Anthropic ran this on themselves. Their system prompt carried detailed code review and verification instructions. Unnecessary for most requests, crucial for a few. Rather than pay for it every time, they moved verification and code review into skills Claude calls selectively. The information did not get worse. It stopped being mandatory reading.

The mechanics are worth knowing precisely, because they tell you what each layer actually costs.

- Skill descriptions load at startup, one line each, so Claude knows what it can invoke. The full body loads only on use.
- Rules in `.claude/rules/` with a paths pattern load automatically when Claude reads a matching file, and cost nothing until then.
- A skill marked `disable-model-invocation` stays completely out of context, not even its description, until you invoke it by name. That is the right setting for anything with side effects. Committing, deploying, sending messages.
- For the project CLAUDE.md, Anthropic's own guidance is a hard number. Keep it under 200 lines, and move reference content into skills or path-scoped rules.

What earns its place:

- A one-line statement of what the repo is for.
- The unwritten conventions nobody wrote down but everybody follows.
- The thing that looks like a bug and is not.
- The decision Claude would otherwise violate reasonably, like all shared types living in one monolithic file.
- Pointers to the deeper guides, not the guides themselves.

What does not earn its place is anything Claude can learn by listing your directories. Your framework, your test runner, your folder layout, your language version. Claude reads the file system. Describing the repo is the single fastest way to a thousand-line CLAUDE.md that tells Claude nothing it did not already know.

One mechanic almost nobody knows, which should change how you write skills.

After a session compacts, invoked skill bodies are re-injected. But capped at 5,000 tokens per skill and 25,000 tokens total, with the oldest dropped first. Truncation keeps the start of the file. So put your most important instructions near the top of every SKILL.md, and split long skills into branch files. A skill that buries its critical rule on line 400 will silently lose it in any long session.

---

## Defer the tools too

Progressive disclosure is not only for documents.

By default Claude Code loads MCP tool names only, around 120 tokens. The full schemas stay deferred, and Claude fetches specific ones on demand through tool search when a task needs them.

The consequence is a real change in what you can build. Tool descriptions are context, and a large toolset used to mean a permanently crowded window. Which is why teams pruned useful servers just to stay lean. Deferred loading breaks that tradeoff. Connect many more tools without any of them taxing a request that does not need them.

There is a research finding behind why this works so well. Too many tools with overlapping descriptions actively confuses model selection, the confusion mode from earlier. Applying retrieval to tool descriptions rather than dumping them all in has been shown to improve tool selection accuracy roughly threefold. Deferring is not just cheaper. It picks better.

---

## Send the reading somewhere else

Everything above is the context you author. The harder half is the flood that arrives while Claude works. File reads, tool output, conversation.

File reads dominate. A single source file runs 1,000 to 2,500 tokens. A research task that touches a dozen files will quietly eat a third of your window before Claude writes a line.

Delegate that reading instead. A subagent gets its own separate context window. Its own shorter system prompt, its own copy of CLAUDE.md, the same MCP servers and skills, but none of your conversation history. It reads as many files as it needs over there.

In Anthropic's worked example the subagent consumed 6,100 tokens of file reads and returned a 420-token summary. That ratio is the entire point. You paid 420 tokens for work that would have cost you 6,100.

The instruction is as simple as saying so. "Use a subagent to research this, then fix it."

Isolation has a real cost, and it is worth stating honestly. Anthropic has reported multi-agent setups using up to fifteen times more tokens than chat, because every subagent reloads its own core. You are trading total tokens for a clean main window. Usually the right trade, and always a deliberate one.

---

## Compress on purpose, not on panic

Long sessions fill up. Claude Code handles it automatically.

Compaction runs in two stages, clearing older tool outputs first and summarizing the conversation if that is not enough. So a full window never ends your session.

But automatic compaction guesses at what matters. You can do better by compressing deliberately, before the automatic pass fires.

Two commands, two different jobs.

`/compact` takes an instruction, so "compact, focus on the auth bug fix" keeps what you chose.

`/clear` wipes history entirely, and it is badly underused. If the next task does not meaningfully depend on the last twenty messages, old conversation is pure noise crowding out files you are about to need.

Now the part that catches people. Not everything survives compaction equally.

Project-root CLAUDE.md and auto memory are re-injected from disk. But rules with `paths` frontmatter and nested CLAUDE.md files live in message history. They get summarized away and do not return until Claude reads a matching file again.

If a rule genuinely must persist, drop the `paths` scoping or move it to the project root.

---

## Six context jobs to run this week

1. **Open your window and read it.** Run `/context` at session start, then again after an hour of work. Watch memory files and free space. If memory files are heavy, you just diagnosed an overweight CLAUDE.md in one command.
2. **Get the cut list generated for you.** Run `/doctor` and Claude reads your skills and CLAUDE.md and flags overconstraining absolutes, cross-file contradictions, and split candidates.
3. **Hunt contradictions across files.** Have Claude read CLAUDE.md and every skill together and list pairs of instructions that cannot both be followed. Those are the lines quietly taxing every request you make.
4. **Split the trunk into a tree.** Anything longer than a few lines becomes a skill or a path-scoped rule with a pointer left behind. Target: under 200 lines.
5. **Delegate the reading.** Any research task touching more than a few files goes to a subagent. Check `/context` before and after. The flat line sells it.
6. **Move state onto the filesystem.** Have Claude keep a plan file it rewrites as it works. It survives compaction because it lives on disk, and long tasks stop losing the thread halfway through.

---

## If the model is the CPU, the context window is the RAM.

Every line you add is a line that loads on every request, forever.

Context is not free storage. It is a standing cost.

The architects went the other direction. They measured before they touched anything. They cut four fifths and lost nothing. They swapped prohibitions for principles, scrolls for trees, eager tools for searched ones. And sent the heavy reading somewhere else entirely.

Most people will keep appending to a window they have never opened.

---

Follow me and subscribe to my Telegram channel: https://t.me/+75nMf005jRpjMDU1
