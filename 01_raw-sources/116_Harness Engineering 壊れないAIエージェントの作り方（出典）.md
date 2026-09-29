---
title: "116_Harness Engineering 壊れないAIエージェントの作り方（出典）"
tags: [raw-source]
source: https://x.com/0xwhrrari/status/2093685107534000560
author: whrrari（@0xwhrrari）
published: 2026-08-29
created: 2026-09-29
---

# 出典メタデータ

- URL: https://x.com/0xwhrrari/status/2093685107534000560
- 著者: whrrari（@0xwhrrari）※既存 [[01_LOOP vs GRAPH vs HARNESS ENGINEERING]] と同一著者
- 公開: 2026年8月29日
- 形態: X 長文ポスト（完全ガイド）
- タイトル: **Harness Engineering: How to Build AI Agents That Don't Fall Apart**
- 引用元: Dario Amodei（Anthropic CEO）・OpenAI「Harness engineering: leveraging Codex in an agent-first world」・Anthropic「Harness design for long-running application development」・Anthropic Managed Agents

要約は [[07_Harness Engineering 壊れないAIエージェントの作り方]] を参照。

---

# 原文（@0xwhrrari の X ポスト全文・構造化）

Most people respond to a failing agent by changing the prompt. Then they change the model. Then they add a larger context window. The agent still forgets decisions / uses the wrong tool / skips verification / gets stuck in the same loop.

**The problem is not always the intelligence. The problem is the environment around it. That environment is the harness. And designing it is harness engineering.**

> Dario Amodei, CEO of Anthropic: "**Of course, you need an interface, you need a harness to use them**"（Claude Code の誕生を説明して）

## The model is only the reasoning engine

A model can suggest the next action. It cannot create a reliable operating environment by itself.

```
MODEL: reasons and proposes actions
HARNESS: selects context / exposes tools / stores state /
         enforces permissions / checks results / records traces /
         recovers from failure
```

The prompt is one component inside this system. The model is another. The product is what happens when every surrounding component works together.

> **Prompt engineering improves the instruction. Harness engineering improves the conditions under which the instruction is executed.**

## The same model can become a completely different agent

Put the same model inside a chat box and it answers questions. Put it inside a repository with terminal access, tests, browser tools, project memory, isolated worktrees, and a review loop and it can ship software. **The weights did not change. The harness did.**

OpenAI described the same shift building an agent-first codebase with Codex — early progress was slow because **the environment was underspecified, not because the model lacked raw capability**. The response was not to tell the agent to try harder; it was to ask **what capability was missing** and make that capability legible and enforceable.

## A production harness has seven jobs

### 1. Turn the request into a contract
Before the agent acts, convert the request into a bounded object: goal / inputs / output / constraints / **done_when**（tests pass, visual check passes, review passes）. **The contract protects the task from silent redefinition.** Without it, the agent can complete a different job and still declare success.

### 2. Give the agent a map
Agents need project knowledge, not every document in every context window. Use a small root guide（AGENTS.md）that tells the agent where to look: architecture map / testing map / product rules / security rules / task-specific guides. **A map preserves context. A giant manual consumes it.** Keep detailed knowledge close to the code it governs; load only when the task needs it.

### 3. Expose the right tools inside the right environment
Tool access is an interface between the model and the real world. Every tool needs: clear purpose / predictable output / **explicit failure state** / permission boundary.
```
READ FILES: allowed by default
RUN TESTS: allowed inside sandbox
WRITE FILES: allowed inside workspace
ACCESS NETWORK: scoped by task
DEPLOY / DELETE DATA: requires approval
```
**Good tools reduce ambiguity before the model has a chance to reason badly. Bad tools force the model to guess what happened.**

### 4. Externalize memory into durable state
The conversation is not the system of record. Store decisions, artifacts, failures, and open risks outside the context window: task_id / current_step / artifacts / decisions / failures / pending. **The next session should inherit the state of the work, not a lossy retelling of the conversation.** This is how an agent survives context resets, crashes, and handoffs.

### 5. Add sensors before adding autonomy
An agent cannot correct what it cannot observe. Tests, linters, screenshots, logs, metrics, schema validators **turn vague quality into evidence**.
```
CODE: tests + type checks + lint
UI: render + screenshot + visual inspection
RESEARCH: source check + contradiction check
DATA: schema + range + freshness checks
```
The model creates an artifact. The environment produces evidence about the artifact. The harness decides whether that evidence is enough to continue.

### 6. Enforce permissions outside the model
**MODEL SUGGESTS -> POLICY CHECKS -> TOOL EXECUTES.** This separation matters most when the action is expensive, irreversible, or touches another person. **Do not ask the same probabilistic system to invent the plan, approve the risk, and execute the side effect.**

### 7. Record traces and recover locally
Every run should leave a readable trail: request / selected context / tool calls / state changes / verification results / retries / cost / final artifact / rollback point. **Without traces, failure becomes a mystery. With traces, failure becomes input for the next harness improvement.**

## Instructions should become infrastructure

Most teams keep important rules in prose. The agent reads them, then eventually ignores one. The stronger pattern is to **encode the important rule twice**:
```
GUIDE: "UI code may not query the database directly"
CHECK: lint fails when UI imports the repository layer
```
**The guide explains the reason. The check enforces the boundary.** This turns a past failure into a permanent system improvement. The next agent does not need to remember the incident — **the harness remembers for it**.

## The loop belongs to the harness

```js
for (attempt = 1; attempt <= 3; attempt++) {
  artifact = await build(state)
  evidence = await verify(artifact)
  if (evidence.pass) return artifact
  state.failures.push(evidence.gap)
  state.repair = evidence.repair
}
return requestHumanReview(state)
```
**The model decides how to repair the local gap. The harness decides whether another attempt is allowed.** Anthropic reached a similar conclusion on long-running agents: structured artifacts preserve continuity, a separate evaluator gives concrete feedback instead of letting the builder approve its own work. "Find the simplest solution possible, and only increase complexity when needed."

## Failure should upgrade the system

Most people repair the current output. **Harness engineers repair the class of failure.**
```
MISSING CONTEXT -> add a map or retrieval rule
WRONG TOOL      -> improve tool description or routing
BAD OUTPUT      -> add a validator or stronger contract
REPEATED LOOP   -> add a retry cap and escalation
UNSAFE ACTION   -> add a permission gate
LOST DECISION   -> store it in durable state
UNKNOWN FAILURE -> add tracing and evidence capture
```
The immediate patch fixes one run. **The harness change improves every run after it. That is the compounding advantage.**
> **A good harness converts agent mistakes into infrastructure.**

## Separate the brain, the hands, and the history

```
BRAIN: the model that reasons
HANDS: the sandbox and tools that act
HISTORY: the append-only record of what happened
```
If the sandbox dies, the history survives. If the model changes, the tools and policy remain inspectable. If a task resumes, a new session reconstructs state from artifacts and traces. Anthropic's Managed Agents makes this explicit（session / harness / sandbox）. **The reasoning engine should not also be the filesystem, permission system, memory database, and audit log.**

## Give every run a change receipt

Keep a compact receipt explaining how the output was produced: context_sources / policy_version / model_route / tools_used / tests(passed/failed) / human_corrections / retries / cost_usd / accepted_artifact / rollback_point. This makes **model upgrades comparable, regressions attributable, audits possible** — and prevents the final answer from hiding a broken process.

## Start with the smallest harness that closes the loop

```
LEVEL 0: prompt + model
LEVEL 1: project guide + tools
LEVEL 2: structured state + tests + bounded loop
LEVEL 3: permissions + traces + recovery + human gates
```
**Move up only when the task earns the complexity.** The harness should be smaller than the failure surface it controls.

## The harness engineering checklist（12項）

- [ ] Is success defined before execution begins
- [ ] Can the agent find the right project knowledge without loading everything
- [ ] Does every tool have a clear contract and failure state
- [ ] Is execution isolated from production systems
- [ ] Are important decisions stored outside the conversation
- [ ] Does every risky transition have evidence
- [ ] Are irreversible actions protected by approval
- [ ] Does every loop have a retry cap and budget
- [ ] Can the run resume after interruption
- [ ] Can you explain every tool call and state change
- [ ] Does failure update a guide, test, tool, or policy
- [ ] Can the final artifact be rolled back

If several answers are no, **a stronger model will not make the system reliable. It will only make the failure more expensive.**

## The real shift

```
PROMPT  -> instruction
CONTEXT -> working view
HARNESS -> operating system
LOOP    -> local improvement
GRAPH   -> coordination
```

The model may change next month. **The tools, tests, state, policies, and traces can keep improving.** That is why the durable advantage is moving out of the prompt and into the system around it. The best builders will not only ask which model is smartest — **they will ask which environment makes that intelligence reliable.**
