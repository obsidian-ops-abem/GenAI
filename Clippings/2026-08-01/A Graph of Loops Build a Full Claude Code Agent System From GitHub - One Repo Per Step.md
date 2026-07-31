---
title: "A Graph of Loops: Build a Full Claude Code Agent System From GitHub - One Repo Per Step"
source: "https://x.com/Granite0x/status/2080665298609328201"
author:
  - "[[@Granite0x]]"
published: 2026-07-24
created: 2026-08-01
description: "Every node in the graph is an agent running a loop. The graph coordinates the fleet. The loop makes each one trustworthy.I built the whole C..."
tags:
  - "clippings"
---
![画像](https://pbs.twimg.com/media/HOABn_jW8AALBvm?format=jpg&name=large)

Every node in the graph is an agent running a loop. The graph coordinates the fleet. The loop makes each one trustworthy.

I built the whole Claude Code system by grafting one real GitHub repo per step: bernstein, beads, serena, waku. Read at source.

Everyone online builds the graph or the loop. Nobody wires both.

So I read all ten repos at the source, not the READMEs - the actual scheduler code, hook scripts, loop functions. Graph on top, loop underneath.

One number so you know I opened the files. Claude Code spends about **16,000 tokens** of your context, before you type a character, on built-in tool descriptions you can't see or edit. Step L3 is the repo that takes them back.

Every star count and license below was pulled from the GitHub API the morning this went up. Where a repo is heavier than its pitch or ships a trap, I say so - all ten do.

# The Map

![画像](https://pbs.twimg.com/media/HN_1EPbXEAA7leD?format=jpg&name=large)

**Bookmark it.** 10 repos, two layers, one screen.

**THE GRAPH - coordinate the fleet**

**G1 · Orchestrate** - sipyourdrink-ltd/bernstein · 726 · Apache-2.0 **G2 · Fan out in isolation** - nekocode/agent-worktree · 267 · MIT **G3 · Give each node a role** - wshobson/agents · 38,185 · MIT **G4 · A graph that already ships** - fivetaku/insane-research · 108 · MIT

**THE LOOP - make one node trustworthy**

**L1 · Memory** - gastownhall/beads · 25,603 · MIT **L2 · The loop core** - ShenSeanChen/waku-agent · 440 · MIT **L3 · Context** - oraios/serena · 26,813 · MIT **L4 · Skills** - obra/superpowers · 260,116 · MIT **L5 · The gate** - hamelsmu/claude-review-loop · 706 · no license **L6 · Proof** - raindrop-ai/workshop · 937 · MIT

Here's the line that ties the two layers together. Every node in the graph is an agent running a loop.

The graph decides who runs and when. The loop decides whether you can trust what comes back. Build the graph out of loops you can trust - or you've just built a faster way to ship bugs across a fleet.

<video preload="none" tabindex="-1" playsinline="" aria-label="埋め込み動画" poster="https://pbs.twimg.com/amplify_video_thumb/2080650857243156480/img/aA-nRxDs0zAHtOgu.jpg" style="width: 100%; height: 100%; position: absolute; background-color: black; top: 0%; left: 0%; transform: rotate(0deg) scale(1.005);"><source type="video/mp4"></video>

![](https://pbs.twimg.com/amplify_video_thumb/2080650857243156480/img/aA-nRxDs0zAHtOgu.jpg?name=large)

# THE GRAPH - coordinate the fleet

## G1 · Orchestrate the fleet → sipyourdrink-ltd/bernstein

You have five tasks and want them run in the right order, some in parallel. That's a graph, and something has to schedule it.

bernstein is the only repo here whose scheduler is that graph, in plain Python, with zero model in the coordination loop. core/orchestration/task\_dag.py loads a task DAG - - \[ \] \[T002\] Wire loader -> T001, or YAML with depends\_on: - detects cycles, and walks it one wave at a time. Every task marked \[P\] batches into a concurrent frozenset; the rest run serially.

Each ready node then goes to the real claude CLI in its own git worktree, behind a lint/type/test gate. It merges only if it's green.

<video preload="none" tabindex="-1" playsinline="" aria-label="埋め込み動画" poster="https://pbs.twimg.com/amplify_video_thumb/2080650814926815232/img/g4817tmYJ-pRQ61Z.jpg" style="width: 100%; height: 100%; position: absolute; background-color: black; top: 0%; left: 0%; transform: rotate(0deg) scale(1.005);"><source type="video/mp4"></video>

![](https://pbs.twimg.com/amplify_video_thumb/2080650814926815232/img/g4817tmYJ-pRQ61Z.jpg?name=large)

```text
pip install bernstein && bernstein init && bernstein -g "Add rate limiting"
```

The trap: it's a heavy platform - 1,765 Python files, its own .sdd/ workspace, a bearer-token task server. And the DAG is hand-authored or compiled from a spec; it doesn't infer your graph for you. That's the price of a scheduler that runs the same way every time.

## G2 · Fan out in isolation → nekocode/agent-worktree

Run five agents in parallel in one repo and they trample each other's files. The fix is a git worktree per agent.

agent-worktree is the lightweight CLI primitive for exactly that. create\_worktree runs git worktree add -b \<branch> \<path> \<base>, and the merge path does a **dry-run merge first**. If it wouldn't apply cleanly it aborts with "Merge aborted due to conflicts" and rolls back with reset - so the repo is never left half-merged.

```text
npm install -g agent-worktree      # then: wt run -- claude
```

The trap: it's a per-worktree primitive, not a scheduler. It doesn't decide how many agents to spawn - that's G1's job. It gives each one a clean, isolated tree and a safe way home.

## G3 · Give each node a role → wshobson/agents

A graph of identical agents is just a slow agent. Each node should be a specialist.

wshobson/agents is a real Claude Code plugin marketplace - not an awesome-list of links. It ships **203 specialist subagents** across 94 plugins: a backend-architect, a code-reviewer, each a Markdown file with YAML frontmatter and a real role body. It even tiers them - Opus for the 55 architecture, security, and review roles; Haiku for fast ops.

```text
/plugin marketplace add wshobson/agents      # then /plugin install <plugin>
```

The trap: install only the plugins whose roles you'll actually use. Staffing all 203 into context is how you burn the window you spent Step L3 protecting.

## G4 · A graph that already ships → fivetaku/insane-research

You don't have to take the pattern on faith. One plugin runs the whole thing today.

insane-research is a Claude Code plugin that runs a real 7-phase research graph. Phase 3 fans out 3-5 sub-agents through the Task tool, throttled to 2-3 concurrent - a hard guard against a >16-agent fan-out that trips "Server is temporarily overloaded."

Phase 6 is the part worth stealing. A **deterministic** code gate, scripts/validate\_ledger.py, scores every claim itself and is the only thing allowed to write verified\_claims.json. Fan out, then let code - not a model - decide what survives.

```text
/plugin marketplace add https://github.com/fivetaku/gptaku_plugins.git && /plugin install insane-research
```

The trap: skip the validate step and synthesis has nothing allowlisted to cite. The gate isn't optional decoration - it's the only reason the fan-out is trustworthy.

# THE LOOP - make one node trustworthy

Every node above is one agent running this loop. Here's what goes inside it.

## L1 · Memory → gastownhall/beads

A build isn't a to-do list. It's a dependency graph, and the agent forgets it at every context reset.

beads replaces the markdown TODO with a real graph over versioned SQL. bd ready returns only tasks with no open blockers. bd remember "insight" survives sessions and even account rotations; bd prime re-injects it next session for about **50 tokens**.

Old closed work decays to a 70%-smaller stub - and the agent itself writes the summary, so there's no extra API bill.

```text
npm install -g @beads/bd
bd init && bd setup claude
```

The trap: if you see disk I/O error (522), your .beads/ is on iCloud or Dropbox. Cloud sync corrupts the database. Move it out.

## L2 · The loop core → ShenSeanChen/waku-agent

Everyone talks about "the loop." Almost no one shows you one small enough to read.

This is the smallest repo I'll ever recommend, on purpose. The entire agent loop is one file, waku/loop/agent.py, ~95 lines. Its docstring's first line ends with six honest words: "This file is the whole trick."

The loop has no done flag - it ends when the model stops asking for tools. The only thing stopping an infinite spin is for iteration in range(1, max\_iterations + 1), default **10**.

![画像](https://pbs.twimg.com/media/HN_3x5LXMAAUuOe?format=jpg&name=large)

```text
git clone https://github.com/ShenSeanChen/waku-agent && (cd waku-agent && uv venv && uv pip install -e .) && uv run waku dashboard
```

The trap: it swallows tool errors into a string the model reads as normal output. So a silently-wrong tool looks correct. That's not a bug - it's why L5 exists.

## L3 · Context → oraios/serena

Here's where those 16,000 tokens go, and how you get them back.

![画像](https://pbs.twimg.com/media/HN_3jueXAAAD5gH?format=jpg&name=large)

serena's maintainers measured Claude Code's built-in tool descriptions at ~**16k** un-editable tokens that bias Opus toward reading whole files. Started with --context claude-code, serena forbids Read/Edit for discovery and forces symbol-level retrieval over MCP. It pulls the one function and its references, not the 2,000-line file.

```text
uv tool install -p 3.13 serena-agent && serena setup claude-code
```

The trap: serena verifies nothing - it only hands the model better tools. The strongest refactors, moving a symbol or a file, need the paid JetBrains backend.

## L4 · Skills → obra/superpowers

Skills are the moves a node has. They're also the most overrated layer in your setup.

superpowers is a full methodology, not a pack. Its TDD skill enforces one rule it calls the Iron Law - "NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST" - and if code exists before its test, "Delete means delete." It's built on the anthropics/skills format, where a dormant skill costs ~100 words until its description matches your task.

```text
/plugin install superpowers@claude-plugins-official
```

The trap: it's persuasion, not a syscall. A model can write assert True and claim it saw red. Real enforcement is L5.

## L5 · The gate → hamelsmu/claude-review-loop

An agent that grades its own homework ships its own bugs. You need a second model in the loop.

This is a Stop hook - Claude Code's exit interceptor. When the agent tries to stop, the hook returns {"decision":"block"} and makes it run OpenAI's Codex first: up to four reviewers in parallel - diff plus OWASP, architecture, Next.js, browser. It can't quit until reviews/review-\<id>.md exists on disk.

![画像](https://pbs.twimg.com/media/HN_3RBvWYAAocHL?format=jpg&name=large)

It fails open by design. The source comment reads "never trap the user in a broken loop." Want the spec-driven version instead? npx cc-sdd@latest (3,581 stars, MIT) runs an independent reviewer per task.

```text
npm i -g @openai/codex && claude plugin marketplace add hamelsmu/claude-review-loop && claude plugin install review-loop@hamel-review
```

The trap: no LICENSE file in the repo, and it only checks that a review file exists - the agent is allowed to disagree and skip findings.

## L6 · Proof → raindrop-ai/workshop

You can feel like the loop got better. An eval is how you know - and the best one is itself a loop.

workshop captures a real run, then re-drives that exact trace against your now-edited code on a local daemon (port **5899**), and diffs the tool calls. The agent's assertions are read-only SQL over a local trace DB. So it sees the failure, fixes the code, and re-runs until the spans are green.

```text
# installer is one line in the repo readme:
github.com/raindrop-ai/workshop
# then, inside your agent:
/instrument-agent
```

The trap: replay is only safe if you extract a clean entrypoint. Point it at a production request handler and it can hit your real database, billing, and email.

# Assemble It

Read the map top to bottom and the whole thing snaps together.

bernstein compiles the work into a DAG and spawns a node per task. agent-worktree gives each node an isolated tree. wshobson/agents gives it a role.

And inside every one of those nodes runs the loop. beads remembers, waku iterates, serena feeds it only the code it touches, superpowers gives it the moves, claude-review-loop blocks its exit until a second model signs off, workshop proves it.

insane-research is the whole shape in one plugin you can run tonight: fan out, verify with code, synthesize.

That's the difference between the threads going around and a system. One draws the graph and hopes the nodes work. The other builds the graph out of loops that can't lie to you about being done.

One test grades all ten: can your system take done back?

bernstein refuses to merge a node that fails its gate. beads flips a task back to not-ready. The review hook un-finishes a finished session, and workshop fails a green trace.

A system that can only promote is a burndown chart with extra steps.

So tell me where yours breaks. Which step is missing from your setup right now - G1 through G4, or L1 through L6?

Just the code. I'll give you the smallest fix for that one, and I'll answer every reply.

Draw the graph. But build it out of loops first.