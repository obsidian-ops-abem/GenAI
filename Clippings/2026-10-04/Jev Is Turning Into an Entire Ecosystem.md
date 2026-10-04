---
title: "Jev Is Turning Into an Entire Ecosystem"
source: "https://x.com/hrswatigupta/status/2102741642050666755"
author:
  - "[[@hrswatigupta]]"
published: 2026-09-23
created: 2026-09-29
description: "What happened in the 7 days since launch, which of the 9 new layers are worth your time, and what to build first.On September 15, TypeSafe A..."
tags:
  - "clippings"
---
![画像](https://pbs.twimg.com/media/HS0JQbxaoAEqMhx?format=jpg&name=large)

**What happened in the 7 days since launch, which of the 9 new layers are worth your time, and what to build first.**

On September 15, TypeSafe AI shipped Jev, a model that cannot write a single word. One week later, it has a family tree.

Here is the short version of what I have been watching since Tuesday: an open clone called Laya, a model called Kev you can train on your own MacBook, laya-mlx that runs Laya natively on Apple Silicon, a browser agent that books a flight search in 7 seconds, routers that decide which model each turn of your Codex session gets, local Jev-compatible servers, an independent leaderboard built on 31,500 human-labelled decisions, and computer-use experiments running on macOS, Android, a MuJoCo drone, and Pokemon Red.

If Jev was a tool last week, it looks like a platform this week that nobody planned. This article maps all 9 layers, tells you what actually works, where the marketing numbers stop and the real numbers begin, and gives you a step-by-step weekend plan to put one of these into your own code.

## The 60-Second Refresher: What Jev Actually Is

If you already know this part, skip to the ecosystem map.

Jev is the first model from TypeSafe AI, launched on September 15, 2026 alongside a $40M seed round led by DCVC. The founder, Diogo Almeida, is a co-inventor of the RLHF work behind ChatGPT. TypeSafe's pitch: chat models are optimized to produce text humans enjoy reading. Jev is optimized to produce decisions programs can branch on.

The interface is one request:

- **State**: a string, a JSON object, or an array of text. A ticket, an email, a DOM snapshot, a game state.
- **Typed questions**: questions defined in code, not in a prompt string. Each one declares its answer type up front.
- **Typed answers**: one value per question, each carrying a probability. No text, no JSON parsing, no explanation, no refusals.

There are exactly three question types:

| Type | What it answers | Limit |
| --- | --- | --- |
| Choice | Pick one of N options, returns a probability for every option | Up to 255 options |
| Score | Place the state on an ordered rubric, returns an expected level | 2 to 10 levels |
| Noul | A yes/no probability (P(true)) | One question |

The wire format, based on the public API docs and the Jev-compatible ports that mirror them:

```bash
curl -X POST https://api.typesafe.ai/v1/systemone \
  -H "Authorization: Bearer $TYPESAFE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jev-latest",
    "state": {
      "subject": "Refund not received",
      "body": "I cancelled two weeks ago and still have no refund."
    },
    "questions": {
      "department": {
        "type": "choice",
        "instructions": "Which team should handle this ticket?",
        "criteria": ["billing", "support", "sales"]
      },
      "urgency": {
        "type": "score",
        "instructions": "How urgent is this ticket?",
        "criteria": ["not urgent", "somewhat urgent", "urgent", "critical"]
      },
      "churn_risk": {
        "type": "noul",
        "instructions": "Is the customer likely to cancel or dispute?"
      }
    }
  }'
```

One round trip gives you something like this:

```json
{
  "answers": {
    "department": { "choice": "billing", "probabilities": { "billing": 0.9415, "support": 0.031, "sales": 0.0275 } },
    "urgency": { "score": 1.3886 },
    "churn_risk": { "noul": 0.0988 }
  },
  "usage": { "input_tokens": 267 }
}
```

The specs that matter:

| Spec | Value |
| --- | --- |
| Current build | jev-1.13.0 (alias: jev-latest) |
| Latency (TypeSafe-reported, US West Coast) | 70-500 ms end to end |
| Price | $0.042 per million input tokens, output free |
| Context | 32K tokens for state plus the longest single question |
| Input | Text only: string, JSON object, or array. No image, audio, or video |
| Official SDKs | Python (typesafe-sdk), TypeScript (@typesafe-ai/sdk) |

The training method is RLCD, reinforcement learning for calibrated decisions. Where RLHF trains a model to write text that humans rate highly, RLCD trains a model so that when it says 70 percent, it is right about 70 percent of the time. That is the whole pitch in one sentence: probabilities you can set thresholds on.

![画像](https://pbs.twimg.com/media/HS0N_0PaQAAKisO?format=jpg&name=large)

## Why an Ecosystem Forms Around It (Not Around the Model Name)

Most model launches attract wrappers. The Jev launch attracted an interface.

The difference: chat model launches compete on who is smartest. Decision model launches compete on shape. Jev's request/response shape is small enough to replicate that "Jev-compatible" is already a working label: same endpoint style, same three question types, same answer format. Once the shape is stable, three forces kick in.

**Force 1: lock-in fear.** Jev is a waitlisted, hosted API. No public weights, no on-prem option. If your workflow depends on it, a local fallback is not a nice-to-have, it is a line in your risk register. That is what Laya, Kev, and the Jev-compatible local servers are.

**Force 2: the decision layer fits everywhere in an agent stack.** Every agent has thousands of small choice moments per session: which tool to call next, is the page in the right state, is this diff dangerous, which model should handle this turn. None of them need text generation. That is what the routers, the browser agents, and the guardrail tools are.

**Force 3: somebody has to grade the model.** The evals arrived on day three, and they are the most important layer for anyone making an adoption decision.

The adoption numbers back the momentum: Vercel reports Jev as the fastest-adopted model in AI Gateway history, reaching nearly 13 percent of paid teams within 24 hours, more than 2x the GPT-5.6 family and over 6x Fable 5.1. Jev is free on the Vercel AI Gateway until September 25, which is exactly why this week feels so busy.

![画像](https://pbs.twimg.com/media/HS0N79_b0AA9xa4?format=jpg&name=large)

## The 9 Layers

The full map, before we go layer by layer:

![画像](https://pbs.twimg.com/media/HS0N4DlbUAAryLv?format=jpg&name=large)

| # | Layer | Representative projects | What you get |
| --- | --- | --- | --- |
| 1 | Open clones | Laya, Kev-0.5B/8B, Jevlike, OpenJev | Jev-shaped models with public weights |
| 2 | Local runtimes | laya-mlx, jevmlx, Kev on Apple Silicon | Sub-20 ms decisions with no cloud call |
| 3 | Jev-compatible services | LocalJev, openjev-sglang, open-alternative-jev | Point your existing Jev client at localhost |
| 4 | Browser agents + computer use | jev-ultrafast, jev-browser, typesafe-computer-use, mobile-jev, game demos | Jev inside the action loop |
| 5 | Model routers | jev-router, DSH Codex Router, Jev-Auto-Router | Cheapest model + reasoning depth per turn |
| 6 | Chat + consumer apps | Needle, HA-Jev Assist | Jev as the fast layer under a UI |
| 7 | Evals | jevals.com, JevBench, dayhaysoos/jevals workbench | Human-labelled accuracy and calibration |
| 8 | SDKs + glue | Official Python/TS, Go, Swift, PHP, R, Elixir, MCP servers, SQL extensions | Jev from whatever language you use |
| 9 | Production integrations | Vercel AI Gateway, eve, LangChain harness, duckdb-jev | Jev inside systems you already use |

Layer 1: The Open Clones

Within 48 hours of launch, at least six open clones appeared, per [Latent.Space](https://latent.space/)'s catalogue as summarized by [explainx.ai](https://explainx.ai/). The notable ones:

| Clone | Base / method | Size | Notable |
| --- | --- | --- | --- |
| Laya (Convai Innovations) | ModernBERT-large + PPO | 421M (+322M multilingual) | Jev-compatible API shape; ONNX and MLX ports; T-Rex arena vs hosted Jev |
| Kev-0.5B (Jared Palmer, Cognition) | Qwen2.5-0.5B + LoRA + pointer head | 0.5B (an 8B version followed) | Apache-2.0; trains and runs on a MacBook Pro; played chess vs itself on an M5 |
| Bespoke Nimble | LoRA on Qwen3.5-9B | 9B | 66% to 90% accuracy after a data-curation pass; ~100 ms on H100 (creator-reported) |
| Jevlike | Embedding-only | ~40KB | Tests the floor: how small can "decision" get |
| OpenJev | Open reproduction | 4B/35B | Bigger-footprint alternative |
| DiffusionGemmaJev | Built on DiffusionGemma | \- | Different substrate, same decision output |

Add NanoJev (0.6B, ships with its training pipeline, weights, and dataset) and a Parallel Constrained Decoding research demo on Qwen2.5-1B.

Read the signal correctly: six implementations in two days, from a 40KB toy to a 9B LoRA, means the core idea (small model, typed output, calibrated probabilities) is replicable with off-the-shelf base models and known training methods. That is not an indictment of TypeSafe, it is the point: the category is an idea anyone can implement, and implementation quality is where the differences live.

Two honest gaps to know before you trust any clone:

- Kev answered six questions in about 160 ms on a MacBook, but Jev led by roughly 19 points when the test moved out of domain (per The Unwind's testing).
- Laya's viral "50x faster than Jev" claim comes from a single X post with no disclosed methodology. The repo's own numbers (13.42 ms median latency for the 421M model on an M3 Max) are real and reproducible, but there is no apples-to-apples comparison against hosted Jev in the public record yet.

Layer 2: Local Runtimes

![画像](https://pbs.twimg.com/media/HS0N09ea4AARWSn?format=jpg&name=large)

This is where Apple Silicon users get interesting.

**laya-mlx** (mizorewww) is a native MLX inference runtime for Laya: no PyTorch, no Transformers, no cloud call. The headline numbers come from the repo's own benchmarks:

| Checkpoint | Median latency (M3 Max) | Peak memory |
| --- | --- | --- |
| Laya 421M | 13.42 ms | 943.6 MiB (under 1 GB) |
| Laya 322M multilingual | lower | 687.6 MiB |

The context window is the constraint: 512 or 1,024 tokens depending on the checkpoint, versus 32K on hosted Jev. For routing and triage decisions that is fine. For long documents it is not.

The community is already putting it to work:

- **laya-vs-jev**: a Chrome T-Rex game arena where local Laya and hosted Jev compete on the same frames.
- **laya-ultrafast** (ipenywis): a full local port of Browser Use's jev-ultrafast agent. Same browser, same DOM snapshot, but the decision model is Laya on MLX instead of TypeSafe's API. One env var switches the two (DECISION\_MODEL=laya or typesafe).
- **laya-jev** (kiuckhuang): runs upstream jev-ultrafast against a local Laya sidecar, with a 3-line TYPESAFE\_ENDPOINT override, and ships conformance and smoke tests so the agent runs without any cloud call.

**Kev** is the other local anchor. Jared Palmer (React, Cognition) built it as a LoRA plus pointer head on Qwen2.5-0.5B: the document and all your questions get packed into one prefill, a block-causal mask keeps the questions isolated from each other, and the head emits probabilities directly instead of decoding text. It is Apache-2.0, and the same author has since released an 8B version for people who want more headroom.

Rule of thumb: if your decision fits in 1K tokens and you need it under 20 ms, laya-mlx or Kev is in the running. If you need 32K context or TypeSafe-grade calibration, you stay on the hosted API, or you wait for a better local checkpoint.

Layer 3: Local Jev-Compatible Services

Clones are new models. Compatible services are a different trick: they keep your client, your SDK, and your code, and swap the upstream.

**LocalJev** (githubnext/localjev) is the most complete example so far. It is a Bun/TypeScript server that implements Jev's POST /v1/systemone endpoint and bridges to OpenAI-compatible local backends (the team tested DiffusionGemma on oMLX). Because standard OpenAI APIs do not expose logit reading, LocalJev does the translation: it rewrites the decision request into a classification prompt, parses the model's JSON probability output, retries on malformed responses, and normalizes the vectors into Jev-compliant choices, expected scores, and entropy-based confidence.

The test record: 1,200 requests across five 4-bit models on an M5 Max.

The catch, stated plainly by The Unwind: this is prompted JSON probability output, not Jev's direct-logit approach. Check the calibration on your own data before you trust it in production. The same caveat applies to every service in this layer.

The rest of the layer:

- **openjev-sglang**: Jev-compatible endpoint backed by open models and prefill-only inference
- **open-alternative-jev**: Jev-shaped decision model on your own GPU
- **openjev**: local bilingual probability decisions
- **jevmlx**: Jev-style parallel constrained decisions for MLX models
- **Verdict-open-jev**: ModernBERT decision engine with a WebGPU playground

The usage pattern (works with any of them):

```bash
# 1. Start a local Jev-compatible server (example: LocalJev)
bun install && bun start        # follow the repo's README

# 2. Point your existing Jev client at it. The laya-jev repo did
#    this for jev-ultrafast in 3 lines: a TYPESAFE_ENDPOINT
#    override. Most SDKs expose the same base-URL setting.
export TYPESAFE_BASE_URL=http://127.0.0.1:8787   # port depends on server
export TYPESAFE_API_KEY=not-used-locally
```

Why this layer matters to you: it is the lowest-friction way to test the decision layer in your app before deciding whether hosted Jev is the right long-term dependency. Write the client against the Jev API shape from day one, and the local-vs-hosted choice becomes an environment variable.

Layer 4: Browser Agents and Computer Use

![画像](https://pbs.twimg.com/media/HS0NxuabYAEST5D?format=jpg&name=large)

This is the layer with the most video.

**jev-ultrafast** (Browser Use, MIT) rebuilt their agent loop around Jev. The architecture in one paragraph: every step, the agent reads an atomic DOM snapshot and builds an indexed table of visible controls (buttons, comboboxes, text boxes, with labels and current values). Jev scores two heads against that table in one request: an operation head (CLICK, TYPE\_TEXT, SELECT, SCROLL\_UP, SCROLL\_DOWN, WAIT, DONE, BLOCKED) and a target head (which element). A small text model is called only when the chosen operation is TYPE\_TEXT, to write the actual string. No screenshots in the decision loop.

The receipts:

| Metric | Value |
| --- | --- |
| Google Flights search, Zurich to London | 7.073 seconds, 1x speed, independently verified |
| Jev requests in that run | 17 (median Jev latency 178 ms) |
| Total cost of the run | $0.0039 |
| Median task time vs prior version of the agent | 9.45s to 7.09s (25% faster) |
| Median browser protocol calls per task | 1,092 to 101 |

Setup:

```bash
git clone https://github.com/browser-use/jev-ultrafast.git
cd jev-ultrafast
uv sync
cp .env.example .env
# Add TYPESAFE_API_KEY and TEXT_MODEL_API_KEY (OpenRouter works for the text model)
uv run jev
# Local inspector UI at http://127.0.0.1:8766
```

Read the limitations list before you build on it: a DONE decision is not proof of success (verify the final page separately); shadow roots, iframes, canvas UI, file uploads, pop-up tabs, and nested scrolling regions are out of scope in this version.

Around it:

- **jev-browser** (jkudish): an MIT-licensed MCP server, CLI, and library that drives a real headless browser. Jev picks one action per step from the page's clickable, typeable, and selectable elements, and scores in the same call whether the goal is already met or the run is stuck. It is now in Cline's official plugin collection.
- [rtrvr.ai](https://rtrvr.ai/) (independent test, one run per config): with Jev on, a LinkedIn message task took 31% less time (95.5s to 65.8s) and an Amazon add-to-cart task took 43% less (178.7s to 101.9s). They also logged the probability and confidence for each Jev action, including one case where a 0.95-confidence "Click Send" produced no observable effect and Jev delegated to the LLM instead of retrying blindly.
- **typesafe-computer-use**: a macOS computer-use experiment using OCR plus bounded Jev action selection.
- **mobile-jev**: an Android agent using Mobilerun observations plus bounded Jev actions; the published Uber demo deliberately stops before booking.

And the toy layer, which I keep because it is the fastest way to watch the pattern generalize: Super Mario Bros from structured emulator state, Snake with one typed decision per tick, Pokemon Red with text game state, original StarCraft shareware with recorded action probabilities, a MuJoCo quadrotor at 2.5 Hz, an SO-101 robot arm, a browser stealth game, and a RuneScape harness.

The reality check: on OSWorld 2.0, the best frontier computer-use agent still completes only 20.6 percent of long-horizon tasks. Jev is not fixing that. What it does is remove the slowest part of the agent loop (the model call per action) and replace it with a sub-second bounded decision. Full autonomy is not the product. Cheap fast reflexes inside an agent are the product.

Layer 5: Codex Routers (and the Whole Routing Category)

The cleanest cost story so far: every turn of your coding agent goes through Jev first, and Jev answers three questions.

- **tier** (Choice): is this work mechanical and local, ordinary engineering, or hard and high-stakes. Jev never sees model names, only descriptions of the work.
- **effort** (Score): a four-level rubric for how much step-by-step reasoning the task needs.
- **risky** (Noul): does it touch production, money, credentials, or anything that cannot be undone.

![画像](https://pbs.twimg.com/media/HS0NoVDaIAA89Fm?format=jpg&name=large)

The router then maps (tier, effort, risky) to a model plus reasoning depth, with asymmetric thresholds: a low bar to upgrade (default 0.3 confidence to spend more) and a higher bar to downgrade (0.6 to spend less). You do not save money by downgrading a hard task you are 50 percent sure about.

Projects:

- **jev-router** (flaviusapop): routes each turn in Claude Code, OpenAI Codex, the Grok CLI, and opencode.

```bash
npm install -g @flaviusapop/jev-router
echo "JEV_API_KEY=..." > ~/.jev-router.env
jev-codex        # then use Codex normally; each turn gets routed
```

Cost: roughly 300 ms added to the first request of a turn. Limitation to know: your prompt text is sent to TypeSafe for the routing decision. Nothing else is.

- **DSH | Jev Codex Router**: a community plugin for the DeepSeek Harness web UI. Jev picks from the available Codex routes; you can set a maximum model and an Astra effort limit, and higher efforts ask before they run.
- **Jev-Auto-Router** (miniLV): per-call GPT model routing for Codex. Jev makes one typed Choice over the (model, effort) pairs available on the host, and a local Responses proxy keeps the tool loop intact.
- **jev-model-router** (a Claude Code plugin): the same three-question design with two backends (TypeSafe direct, or Vercel Gateway). Its transcripts show decisions that look like: **jev: tier fast (0.87) - effort low (0.71) - risky 0.02 - 249ms**.

Same idea, broader scope: a LiteLLM-based router (prismhq/jev-router), per-request routing for the Pi coding agent, a local proxy that picks the Claude model and reasoning effort per message (jcm-router), and a skill router that shrinks a system prompt's skill catalog in one forward pass.

Why this category is growing: model routing is the highest-leverage cost control in an agent stack, and routing is a decision, not a generation. Before Jev, routers used heuristics or a cheap LLM wrapped in JSON parsing. Now the decision has calibrated confidence and takes 100-250 ms.

Layer 6: Chat and Consumer Apps

Jev cannot chat. It has no chat interface and no text output, full stop. So what are "Jev chat apps"?

They are consumer apps where Jev is the fast layer under a conversational UI: intent, routing, triage, confidence. The LLM still writes every word. The division of labor:

| Role in the chat app | Who does it |
| --- | --- |
| "What is the user actually asking" (intent) | Jev (Choice) |
| "Which of my features or tools handles it" (routing) | Jev (Choice) |
| "Is this answer safe and correct enough to send" (guardrail) | Jev (Score or Noul) |
| Drafting, summarizing, tool use | LLM |
| Policy, thresholds, human escalation | Code |

Examples that actually shipped:

- **Needle** (The Unwind, open source, Apache-2.0): a new take on Cmd+F. It searches meaning instead of words. You ask "what happens if I cancel?" and Jev scores the page's source sentences, picks the passages that match your intent, and highlights them in the original text. Ships as a Chrome extension plus a React playground for searching your own text.
- **HA-Jev**: a Home Assistant integration that exposes Jev's probabilities, choices, and scores as home entities, plus an Assist conversation agent. "Is the front door locked" becomes a probability, not a chat reply.
- **typesafe-playground**: TypeSafe's own interactive playground, running small Jev experiments from routing a support message to steering a car in a 3D world.

If you build chat apps: the interesting design question is no longer "which LLM" but "which 20 decisions in the conversation loop can run in 100 ms instead of 2 seconds." Intent, routing, confidence gating, escalation. That is where the latency and the cost live.

Layer 7: Evals (and a Name Collision Worth Knowing)

The layer that tells you what to trust.

[jevals.com](https://jevals.com/) is independent (not affiliated with TypeSafe) and does what most model marketing will not: grade against human labels, not against a reference model. 31,500 decisions, 7 models, 3 question types. The current numbers (2026-09-18 release):

| Board | Task | Best model (Decision Score) | Jev | Jev cost per 1,000 decisions |
| --- | --- | --- | --- | --- |
| Noul | PubMedQA (yes/no) | Gemini 3.8 Flash (73.0) | 69.0, tied for 1st on price | $0.029, p95 653 ms |
| Choice | Banking77 intents | Gemini 3.8 Flash (74.1) | 67.8, tied for 2nd of 8 | $0.043, p95 693 ms |
| Score | HelpSteer2 helpfulness | (no model clearly beats guessing) | 9.2, tied for 1st of 8 | $0.036, p95 670 ms |

The other five LLMs, for completeness: GLM-5.3 at 60.6 / 66.8 / 7.8, Qwen3.8 Flash at 62.4 / 62.4 / -1.4, Mistral Medium 3.5 at 58.0 / 59.5 / -13.7, Mercury 2.5 at 55.7 / 54.0 / -5.5, DeepSeek V4.1 Flash at 47.5 / 63.9 / -19.0. A Decision Score of 0 means guessing the base rate.

Three takeaways:

1. On yes/no and choice questions, Jev is top-2 of 7 at a fraction of the price. The speed-and-price pitch survives independent measurement.
2. On ordered-rubric scoring, nobody beats guessing yet. Jev's 9.2 is the best number on the board, but 9.2 is not production-ready.
3. The p95 latencies (653-693 ms) are the honest numbers to plan around, not the 70 ms from the launch post.

The data is CC-BY-4.0 on GitHub (Jevals/jevals-data), and every number on a board is recomputable from the per-decision logs. That is the bar for an eval.

**JevBench** is TypeSafe's own benchmark for decision models (Jev reportedly leads at 75.3). A vendor topping its own leaderboard is expected, not proof; but it was the first purpose-built benchmark for the category, and decision models had nothing before it.

Name collision alert: there is also dayhaysoos/jevals, a completely different thing. A local evaluation workbench for testing your own Jev questions against examples with expected answers, run with **npx jevals**. Same name, different job: [jevals.com](https://jevals.com/) grades public models, the workbench grades your questions. Both are worth knowing.

How to use this layer: before you trust any clone (Kev, Laya, LocalJev), run it on your own domain data with the same three question types and compare accuracy plus calibration against hosted Jev. The jevals workbench is built for exactly that.

Layer 8: SDKs and Glue

The boring layer that decides whether Jev lands in real codebases:

| Ecosystem | Projects |
| --- | --- |
| Python | typesafe-sdk (official), jevclient (async) |
| TypeScript | @typesafe-ai/sdk (official), neurolink (40+ providers) |
| Go | jev-go, typesafe-go |
| Swift | Official Swift 6 client |
| Java | jev-spring-boot-starter (Spring Boot 4) |
| PHP | laravel-typesafe-jev (typed responses, test fakes) |
| Elixir | jev (GenServer client, pattern-match on the answer) |
| R | jevr |
| Scala | zio-typesafe-ai |
| SQL | sqlite-jev, duckdb-jev, pg-jev, jevsql |
| MCP | jev-mcp (three independent servers), decide-mcp, openrouter-jev-mcp |
| Agents | typesafe-ai/skills (official agent skill), MCP servers for Cursor and Codex |
| Curation | awesome-jev (five active forks maintaining project lists) |

Standouts:

- **duckdb-jev**: a native extension that applies Jev decisions directly to SQL rows. Measured at 1,943 rows/s for 1,000 Choice classifications with confidence, bounded concurrency.
- **sqlite-jev**: a loadable C extension plus Python package exposing Noul, Choice, and Score as SQL functions and batched virtual-table queries.
- **system-one-adapter-python**: TypeSafe's official open-source adapter that runs Jev's System One decision evals across OpenAI- and Anthropic-compatible LLM APIs. This is the official tool for "what does my current LLM do on decision-shaped tasks?"
- **typesafe-ai/skills**: install with **npx skills add typesafe-ai/skills**, then point Claude Code or any skill-compatible agent at your repo and ask where Jev belongs.

The prompt I would actually run against a codebase (works with any agent, or paste it directly):

```plaintext
Find "Jev-shaped" decisions in this codebase: places where the code must
repeatedly (a) choose between a small set of options, (b) score something
on a rubric, or (c) answer a yes/no question, and currently does it with
an LLM call, a regex, or hand-written if/else.

For each candidate output:
1. File and function
2. The decision in one sentence
3. The typed question(s) to ask (Choice / Score / Noul)
4. The state to send
5. Expected calls per day
6. What happens when the answer is wrong

List candidates only. Do not propose refactors.
```

Layer 9: Where It Shows Up in Production

- **Vercel AI Gateway**: typesafe-ai/jev is reachable through the AI SDK's experimental evaluate interface, free until September 25. Fastest-adopted model in gateway history (nearly 13% of paid teams in 24 hours).
- **eve** (Vercel's agent engine) ships Jev as the default evaluation model in its experimental evaluate path; the Vercel Labs AI CLI can run it too.
- **LangChain** published a walkthrough of wiring Jev into an agent harness as the decision layer; a follow-up harness test reported 2,000 expense reports categorized in 20 seconds for five cents.
- **Notra**: a production GEO platform that routes brand-visibility classifiers off an LLM and onto Jev booleans at a 0.5 threshold, targeting 300 ms p50.
- **Guardrail tooling**: jev-git (sub-second Git pre-commit and pre-push gate that screens staged diffs for secrets and destructive commands), limpet (a stop hook that keeps an agent from finishing too early by judging plain-language completion rules), Canny (an evidence ledger that challenges unsupported "done" claims from coding agents), opencompany (gates workspace actions through typed decisions), pi-heed (checks every side-effecting tool call against what the user actually asked for).
- **Context7** (independent testing): Jev won page classification (85% vs Gemini Flash's 56%), tied three of five parsing jobs, and collapsed on crawl-root selection (27% vs 93%). It was still 10-170x faster and 3-20x cheaper. Their summary: great for bounded page-level calls; keep the bigger model around when the task needs a mental map of the whole site.

## Step by Step: Your First Jev Experiment

The full weekend plan. It works with hosted Jev (free via Vercel AI Gateway until September 25) or local (Kev or laya-mlx on a Mac).

**Step 1: Pick one decision in your existing code.** Not a new product. A decision you already make. Good candidates:

- Support ticket routing (which department)
- Is this user message about billing? (Noul)
- Classify an error log into a fix category (Choice)
- Score a draft on a 4-level quality rubric (Score)
- Is this agent output safe to send? (Noul)

A good candidate has three properties: called often, bounded options, and cheap to be wrong about.

**Step 2: Define the state and the questions.** Write the questions first. Bad questions are the number one reason Jev underperforms in practice, and they are the part you fully control. A good Noul question names the evidence ("Is the customer asking for a refund of money already spent?"). A bad one is vague ("Is this urgent?"). For Choice, make the criteria descriptions of what to look for, not just labels.

**Step 3: Call it.** Use the curl example from the top of this article, or the Python version:

```python
import os
import requests

def decide(state, questions):
    r = requests.post(
        "https://api.typesafe.ai/v1/systemone",
        headers={"Authorization": f"Bearer {os.environ['TYPESAFE_API_KEY']}"},
        json={"model": "jev-latest", "state": state, "questions": questions},
        timeout=2,
    )
    r.raise_for_status()
    return r.json()

ticket = {
    "subject": "Refund not received",
    "body": "I cancelled two weeks ago and still have no refund.",
}
questions = {
    "department": {
        "type": "choice",
        "instructions": "Which team should handle this ticket?",
        "criteria": ["billing", "support", "sales"],
    },
    "churn_risk": {
        "type": "noul",
        "instructions": "Is the customer likely to cancel or dispute?",
    },
}

ans = decide(ticket, questions)["answers"]
print(ans["department"]["choice"])     # billing
print(ans["churn_risk"]["noul"])       # 0.0988
```

**Step 4: Add a threshold and an escalation path.** The calibrated probability is the product; the raw answer is not. A production branch looks like this:

```python
p = ans["department"]["probabilities"]
best = max(p, key=p.get)
if p[best] >= 0.85:
    route_to(best)                          # confident: auto-route
elif p[best] >= 0.60:
    route_to(best, flag_for_review=True)    # borderline: route, sample for review
else:
    send_to_human()                         # unclear: do not guess
```

Pick the thresholds from your own logs, not from examples. Log the probability of every decision from day one.

**Step 5: On a Mac, run the local lane in parallel.**

```bash
git clone https://github.com/jaredpalmer/kev
cd kev
# Follow the README: install dependencies, start the TypeSafe-compatible
# server, then point your client's base URL at it (same code as Step 3).
```

You now have an A/B: same questions, same state, hosted Jev versus local Kev, and you can see the accuracy and latency delta on your own data. If you prefer the encoder route, use laya-mlx instead: **uv run hf download aac6fef/laya-typed-decisions-mlx**, then run the laya-mlx quickstart.

**Step 6: Measure like jevals measures.** For the 200-500 labeled examples you can collect, track:

- Accuracy per question type
- Calibration: bucket the probabilities. When Jev said 70-80%, was it right about 70-80% of the time?
- Cost per 1,000 decisions
- Latency p50 and p95

That is the whole eval. dayhaysoos/jevals (**npx jevals**) does the UI part for you.

## How to Read the Noise

A short list of claims to discount when you see them this week:

| Claim | What to do instead |
| --- | --- |
| "440x cheaper than LLMs" (TypeSafe, week two; it was "400x" in week one) | No methodology change was published between the two numbers. Use jevals.com's per-1,000-decision prices |
| "Jev leads JevBench (75.3)" | TypeSafe built JevBench. Read it as "first benchmark for the category," not "independent proof" |
| "50x faster than Jev" (about laya-mlx) | One viral X post, no methodology. Use the repo's own 13.42 ms M3 Max number |
| "Cannot hallucinate" | True in the narrow sense: it cannot emit text outside the schema. A schema-valid answer can still be factually wrong |
| Local "Jev-compatible" server numbers | Prompted JSON output, not direct logits. Rerun calibration on your own data before trusting it |

Two hard limits to design around:

- Choice is capped at 255 options. Bigger sets need staged selection (score first, then choose), which adds a call.
- Context is 32K on the hosted API or 512-1,024 tokens on Laya checkpoints. Jev decides about the state you give it. Retrieval is still your job.

## What to Do Next Week

1. Run the "Jev-shaped decisions" prompt against your own repo. Expect 3-10 candidates.
2. Prototype the highest-volume, lowest-stakes one. Hosted Jev is free on Vercel AI Gateway until September 25, which is all the access you need this week.
3. Log every decision, probability, and latency for 7 days.
4. Set one threshold and one human-escalation path.
5. Watch three signals for the rest of the story: the next [jevals.com](https://jevals.com/) release (do clones close the gap?), the awesome-jev lists (is the glue maturing?), and whether TypeSafe ever ships weights (that would shake up layers 1-3 overnight).

## The Takeaway

One week in, Jev the model is probably not the most interesting part. The most interesting part is that a slot opened in the stack: the fast, cheap, calibrated decision layer, called thousands of times an hour, writing no text at all. Six teams have already shipped different models for that slot, and the browser agents, routers, and evals show up whether the model name on your bill is Jev, Laya, or Kev.

Build against the decision shape, not the vendor. That is how you keep whatever wins.

## Stay in the Loop

That is the state of the Jev ecosystem as of September 22, and it will move this fast for a few more weeks. I break down things like this weekly: what shipped, what actually works when you run it, and what you can run on your own laptop.

**Subscribe to Byte Builders:** [https://bytebuilders.beehiiv.com/subscribe](https://bytebuilders.beehiiv.com/subscribe)

One email a week. No fluff, no vendor benchmarks rehashed, just what you can run.