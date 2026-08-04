---
title: "84_Memory Engineer 4研究所の記憶設計15ステップ（出典）"
tags: [raw-source]
source: https://x.com/N01ennn/status/2083971749079581120
author: N01ennn（@N01ennn）
published: 2026-08-03
created: 2026-08-04
---

# 出典メタデータ

- URL: https://x.com/N01ennn/status/2083971749079581120
- 著者: @N01ennn
- 公開: 2026年8月3日（Xポスト・長文）
- 出典研究: Stanford（Agent Memory）・Microsoft（PlugMem/Memento）・Anthropic（Built-in Memory for Claude Managed Agents）・Nvidia（H100/vLLM/B200）

---

# 本文

![画像](https://pbs.twimg.com/media/HOvBNunXMAA3pVd?format=jpg&name=large)

- How to be a Memory Engineer, from the perspective of Stanford, Microsoft, Anthropic and Nvidia
- Four labs, one job: how to be a Memory Engineer
- Your agent's memory problem is not that it forgets. It's that it never forgets on purpose
- What Stanford, Microsoft, Anthropic and Nvidia know about engineering memory

![画像](https://pbs.twimg.com/media/HOvAJWTWEAAh35J?format=png&name=large)

The most expensive thing your agent does with memory is the part you never watch. You tuned retrieval. You measured accuracy. You never priced the write path, and you never noticed your store forgets nothing

Everyone building agent memory optimizes what the agent remembers. Almost nobody engineers what it costs to build, what is worth keeping, who can delete it, and where it hits the hardware. That gap is the job

This is that job, broken into steps. Six themes, fifteen steps, four labs. Read it top to bottom and you stop being a storer

## See memory for what it is

Step 1: stop calling storage "memory"

You can already give an agent memory. Turn on a vector database, pipe in the history, retrieve top-k, done. That works right up until the history outgrows the context window, the write path costs more than the queries, and the store fills with stale state nobody removes

Real memory is not a store, it is a system with a metabolism. It eats energy on the way in, grows every session, rots if nothing prunes it, and will serve you a memory that was true six months ago and is wrong today. The first step is to stop treating it as a bucket and start treating it as a system with a cost and a lifecycle

Step 2: learn the four lenses

Four labs each answer one hard question about that system. Hold all four at once

- Stanford: what does remembering cost
- Microsoft: what is worth keeping
- Anthropic: who controls what it keeps
- Nvidia: where does it hit the hardware

None of them is wrong. The skill is refusing to pick just one

![画像](https://pbs.twimg.com/media/HOvAf35WgAAiofB?format=png&name=large)

# Price it before you build it (Stanford)

Step 3: move your attention to the write path

Stanford ran the first real systems study of agent memory, and the finding is uncomfortable. The cost does not live where you look

Everyone watches query time, the latency the user feels. The real bill is paid at construction, the write path that turns raw history into stored records, which the user never sees

```text
COST OF A MEMORY SYSTEM
  construction  LLM prefill + embedding, paid once, invisible to users
  query         retrieval + generation, paid every time, the part you watch
  maintenance   dedup, compaction, forgetting, usually missing entirely

FINDING: for LLM-mediated systems, construction burns more energy
         than answering 300 queries against the memory afterward
```

Step 4: measure energy per correct answer, not accuracy

Accuracy hides the bill. Normalize energy by correct answers and two systems with identical accuracy split by 47 times

Same task, same GPU, a 47x gap that no accuracy benchmark will ever show you. From now on, every memory system gets two numbers, quality and cost per correct answer, and you never quote the first without the second

Step 5: pick your cost, there is no best system

Stanford sorts memory into four families: raw context, flat retrieval, structured extraction, and fully agentic. None of them wins on build cost, query speed, and accuracy at the same time

A system like Mem0 answers in under a tenth of a second but pays thousands of seconds to build. A lexical index builds instantly but is slower and blunter at query time. So a Memory Engineer does not pick the best system, they pick which cost to pay on purpose

# PART 3 · Decide what is worth keeping (Microsoft)

Step 6: store facts and skills, not logs

Microsoft's PlugMem work starts with a result that should unsettle you. Giving an agent more raw memory can make it worse, because history piles up, retrieval drowns, and the agent burns its attention wading through transcripts for the one line that mattered

The fix is stolen from human memory. We do not replay events, we keep the facts and the skills we pulled out of them

```text
DON'T STORE THIS
  "May 12, user said: yeah I always ship through GitHub Actions,
   never by hand, learned that the hard way after the prod incident..."

STORE THIS
  fact:  user deploys via GitHub Actions, never manually
  skill: on deploy failure, check the Actions run before touching prod
```

Step 7: judge memory by utility, not size

Stored as facts and skills, one general-purpose memory module beat purpose-built designs across three different tasks while spending fewer tokens. The lesson is a metric

Optimize how much decision-relevant information reaches the agent per token of context it costs, not how much you managed to store. Density beats volume, every time

Step 8: let the model manage its own context

Microsoft's Memento pushes memory inside the model. It reasons in blocks, writes itself a dense note, then deletes the raw reasoning, so peak memory drops 2 to 3x and throughput nearly doubles

Two things a Memory Engineer takes from it. First, this is a learned skill from ordinary fine-tuning, not orchestration bolted on. Second, the erased reasoning does not fully vanish, a shadow survives inside the model, and rebuilding context from the note alone costs 15 points of accuracy. Forgetting is not deletion, and remembering is not just storage

# Keep control of what it keeps (Anthropic)

Step 9: put memory in files you can delete

Anthropic's move is almost boring, and that is the point. Make memory files on a filesystem, so the agent reads and writes it with the same tools it already uses

The reason it matters is everything files make possible: export, inspection, and programmatic control over exactly what the agent retains. A store you cannot open and edit is a store you do not control

Step 10: scope, audit, and roll back

A wrong memory does not fail once, it persists into every future session that reads it. So control is not a layer on top of memory, it is the design

```text
/memory
  /org        read-only     conventions.md, past-incidents.md
  /user-4821  read-write    preferences.md, skills/
  audit.log   which agent, which session, what changed, when
              -> export, roll back, or redact any memory
```

Scope who reads and who writes, keep an audit trail of what was learned and where, and keep the power to reach in and delete. Done right this is measurable: teams building this way cut first-pass errors by 97 percent and sped verification by about a third, because the learning stayed observable the whole time

# Make it survive the hardware (Nvidia)

Step 11: read memory as KV cache, not text

Strip away the algorithms and every memory decision lands on the GPU. Keeping the full history in context is not just slow, it is quadratic, and the prefix caching that saves you inside a session collapses across sessions

All of that pressure concentrates on one scarce resource, the KV cache in high-bandwidth memory. A Memory Engineer reads memory in HBM bandwidth, GPU utilization, tokens per second, and KV slots freed, because underneath every clever scheme the real currency is cache

```text
FULL CONTEXT     cost grows with length squared, KV cache fills HBM,
                 evicted between sessions, so you pay again next time

MEMENTO ON vLLM  finish a reasoning block, flush its KV entries,
                 return the freed slots to the pool

RESULT (B200)    4,290 tok/s vs 2,447 vanilla, same batch 693s vs 1,096s
```

Step 12: treat construction as a background job

Construction is almost pure prefill, long reads in and short writes out, so it behaves like a background indexing job. Co-locate it with live queries and a big write will stall the scheduler exactly when a user query arrives

Rate-limit it, batch it, or defer it, and keep it off the latency-sensitive path. You are not saving text, you are freeing cache for the queries that matter

# PART 6 · Build it without hurting yourself

Step 13: prove each pass by hand first

Before you schedule anything, run it once. Point an agent at your real history, ask it to pull the facts and skills, flag the contradictions between them, and price what it costs to keep them fresh

If the output genuinely changes a decision, it earns a schedule. A memory system that runs against three notes will hallucinate connections that are not there and train you to ignore it, so prove each pass by hand, then automate

Step 14: add a forgetting policy before the store grows

None of the systems Stanford tested prunes or forgets by default, so footprint only grows, up to 9x apart across systems at a million tokens, with agentic systems compounding as the store itself gets larger. Growth slope, not starting size, is what bankrupts a long-lived agent

Add dedup, consolidation, and an explicit forgetting rule before the store gets big. And never auto-merge contradictions: two memories that disagree may both have been right in different contexts, so the system surfaces, you decide

Step 15: ship in this order

- Build the write path first, storing facts and skills, and let it fill for a few weeks so there is real material
- Add contradiction detection by hand a few times, and schedule it only if the collisions surprise you
- Add the forgetting and maintenance policy before volume climbs
- Tune the hardware layer last, once volume is real: batch construction, cap retrieval, watch the KV cache

Do not schedule everything on day one. Get one manual run reliable, wrap it, then automate it

# What this actually means

Every memory system promises the same thing. It never forgets, it notices patterns, it becomes a living store of everything you fed it

Fine. That is the easy half of the job, the remembering

The Memory Engineer works the other half. Not "here is everything I kept" but "here is what I chose not to keep, what I distilled, what I pruned before it rotted, and what I flushed so the next batch would fit". Stanford, Microsoft, Anthropic and Nvidia are four vocabularies for one act, deciding what to let go of

Your agent's problem was never that it forgets. It is that it never forgets on purpose. A storer optimizes what a system remembers, a Memory Engineer optimizes what it forgets, and that is the whole shift

You do not become one by giving your agent a bigger memory. You become one the moment you start engineering its forgetting

Sources: Stanford (Agent Memory: Characterization and System Implications), Microsoft Research (PlugMem, Memento), Anthropic (Built-in Memory for Claude Managed Agents), with the hardware framing from the H100, vLLM and B200 setups in the Stanford and Memento papers