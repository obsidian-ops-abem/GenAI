---
title: "How to Turn One Chat Into a Team of Agents: The 12-Step Opus 5 Course"
source: "https://x.com/0x_rody/status/2082827782732632191"
author:
  - "[[@0x_rody]]"
published: 2026-07-30
created: 2026-07-31
description: "One chat does everything and drowns in its own context. A team splits the work, keeps each window clean, and costs less than the single sess..."
tags:
  - "clippings"
---
![画像](https://pbs.twimg.com/media/HOewnEFXMAAtGyh?format=jpg&name=large)

One chat does everything and drowns in its own context. A team splits the work, keeps each window clean, and costs less than the single session it replaces.

**Inside**: 12 steps from your first subagent to a three-level tree, with the model and effort routing at every layer.

Set it up once and one message runs the whole thing.

**Here's the full course** 👇

**Before we dive in**, I share daily notes on AI & vibe coding in my Telegram channel: [https://t.me/zodchixquant](https://t.me/zodchixquant) 🧠

![画像](https://pbs.twimg.com/media/HOevLskWIAEp3iH?format=jpg&name=large)

## Why teams beat one long session

A single chat carries everything it has ever read. Every dead end, every file it opened and discarded, every wrong turn stays in the window and competes for attention with the thing you actually asked about.

A subagent runs in its own context window with its own system prompt. It reads what it needs, does the job, and hands back the answer. The noise never touches your conversation.

That's the whole idea, and it's why depth matters. Two levels means the noise from the noise also stays away from you. Anthropic has been tuning exactly this: nesting shipped in June capped at five levels, got disabled entirely on July 21, and came back three days later at a default depth of three.

## Phase 1: Your first subagent

**Step 1. Understand what you're buying.** A subagent is not a longer prompt. It's a separate context window you can send somewhere messy, so the mess doesn't come back with the answer.

**Step 2. Write one file.** Subagents live in **.claude/agents/** as markdown with frontmatter:

```markdown
---
name: finder
description: Locates files and code relevant to a task
model: claude-sonnet-5
effort: low
tools: Read, Grep
maxTurns: 8
---

Find what was asked for. Report file paths and
three lines of context each. Never edit anything.
```

**Step 3. Give it the smallest job you have.** Finding files, running a check, summarizing a directory. Watch one full run before you build anything bigger, because this is where you learn what comes back and in what shape.

## Phase 2: Making it a team

**Step 4. Split by workstream, not by task.** The instinct is to spawn an agent per task, which just moves your todo list somewhere else. Split by area instead: the API changes, the tests, the docs. Each owner can then split their own area further.

**Step 5. Add an orchestrator that never touches files.** Its only job is to divide, delegate and assemble:

```markdown
---
name: orchestrator
description: Splits large tasks and assembles results
model: claude-opus-5
effort: high
tools: Read, Grep, Agent
maxTurns: 30
---

Split the task into independent workstreams.
Delegate each one. Never edit files yourself.
Assemble the final answer from what comes back.
```

**Step 6. Route models and effort per role.** This is the money lever. Opus 5 runs $5 per million input and $25 output, half of Fable 5, and the effort dial gives you a second axis on top:

```bash
export CLAUDE_CODE_SUBAGENT_MODEL=claude-sonnet-5
```

Then promote only what needs it through frontmatter. Orchestrator on Opus 5 at high effort, because its decisions propagate to everything below. Workers on Sonnet 5 at low effort, because finding files is mechanical.

Running the whole tree at one setting is how a team gets expensive without getting better.

## Phase 3: Depth and control

**Step 7. Add the middle layer only when a workstream splits twice.** A lead owns an area end to end and delegates the mechanical parts. If a lead passes down something as vague as what it received, you don't need that lead.

**Step 8. Cap the depth structurally.** Remove Agent from your worker's tools. A subagent without that tool cannot spawn anything, no matter what the global setting says. The environment variable **CLAUDE\_CODE\_MAX\_SUBAGENT\_SPAWN\_DEPTH** is a backstop, not the design.

One trap worth knowing: the allowlist syntax that restricts which agents can be spawned only applies when an agent runs as the main thread. Inside a nested definition it is silently ignored, so never rely on it as a safety boundary.

**Step 9. Put limits on everything.** maxTurns on every definition, because a worker with no ceiling can loop on a stuck task until something else stops it. Eight turns is plenty for a mechanical job, thirty for an orchestrator.

## Phase 4: Quality and scale

**Step 10. Add a verifier that didn't do the work.** The maker never grades itself. A separate role checking output against the original requirement catches a category of error that no amount of prompt tuning will:

```markdown
---
name: verifier
description: Checks work against the original requirement
model: claude-opus-5
effort: medium
tools: Read, Grep, Bash
maxTurns: 10
---

Compare the result against the requirement, not against
what looks reasonable. Report pass, fail, or unclear
with the specific evidence for your verdict.
```

**Step 11. Watch one full run.** Use --forward-subagent-text in stream-json mode and nested subagents appear in the output, keyed to whatever spawned them.

Otherwise deep work is invisible until it finishes, and you learn nothing about where time and tokens went.

**Step 12. Prune the team.** Most tasks need two levels, not three. The teams getting real value run an orchestrator and workers most days, add leads when a task genuinely splits twice, and keep the expensive model only at the top.

Depth 3 is a ceiling, not a target.

## Common mistakes

- **Building three levels for a two-level job.** Extra layers add prompt overhead and handoff loss for nothing. Add a lead when the work demands it, not because the feature exists
- **One model, one effort, everywhere.** Then you're paying orchestrator rates for file lookups, and the entire savings of a team structure disappears
- **Leaving Agent in worker tools.** One vague instruction and your leaf node spawns children you never planned for. Structural limits beat settings
- **No verifier.** A lead that forwards worker output without checking it is a relay, not a lead. Verification is the reason that role exists
- **Never watching a full run.** Teams fail quietly in the handoffs, and you cannot fix what you have never seen

## The 20-minute setup

1. Write one worker file and run it on a real lookup task (6 min)
2. Add the orchestrator with Agent in its tools (5 min)
3. Set the subagent model env var to your cheap default (2 min)
4. Remove Agent from the worker so depth caps at two (1 min)
5. Run one real task with --forward-subagent-text and read the whole trace (6 min)

The upgrade isn't more agents. It's that the expensive model finally spends its tokens on decisions instead of file lookups, and everything noisy happens somewhere you never have to read.

**Thanks for reading!**

I share daily notes on AI & vibe coding in my Telegram channel: [https://t.me/zodchixquant](https://t.me/zodchixquant) 🧠

![画像](https://pbs.twimg.com/media/HOev6noXUAAOS84?format=jpg&name=large)