---
title: "Stop Vibe Coding: How to Get Perfect AI Code Every Time"
source: "https://x.com/0xJeyx/status/2082452097740505509"
author:
  - "[[@0xJeyx]]"
published: 2026-07-29
created: 2026-08-02
description: "Have you ever wondered how real engineering teams ship code with AI? It is not a better prompt. It is one document.They are not vibing and h..."
tags:
  - "clippings"
---
![画像](https://pbs.twimg.com/media/HOUEKYLXkAAz88v?format=jpg&name=large)

## Have you ever wondered how real engineering teams ship code with AI? It is not a better prompt. It is one document.

They are not vibing and hoping the model gets it right. They write that document first, and they know exactly what they are going to get before a single line of code exists.

The approach is called spec-driven development, and it is the reason their AI output is predictable while yours is a coin flip.

Look at what the industry shipped rather than what it said. Amazon built Kiro, an IDE designed around specs. GitHub released Spec Kit as an open-source toolkit. Google published a spec-driven codelab for Antigravity. Microsoft has been running posts on it across its developer blog. Four of the largest engineering organisations on earth did not write think pieces about this. They shipped tools.

By the end of this article you will know what actually goes inside a spec, the three documents everyone keeps confusing, a complete spec for a real feature, the five-step loop that turns it into shipped code, and the cases where writing one is a waste of your time.

Before we get into it, follow me on X where I post AI builds and workflows every day.

# 1 - Why vibe coding stops working

Start with what everyone already does. You open your coding agent, describe the app you want, and let it run. The model reads your prompt, guesses what you meant, and writes code.

That first pass is genuinely good for a lot of things. Boilerplate. A quick script. Something on screen in thirty seconds. Nobody should give that up.

The problem starts when the result is not quite right. You edit the prompt. You ask for a different library. The agent rewrites. You go back and forth until it looks close enough to ship.

Here is what actually happened in that loop. The model made a hundred small decisions you never saw, and nothing recorded why. Run the same prompt a hundred times and you get a hundred different builds. None of them are wrong exactly. They are just not the one you had in your head.

Take a request as ordinary as "add authentication." Sounds specific. It is not.

Which token strategy? How long do sessions last? Is there a refresh flow? Password reset? Social login? Which database, which ORM, which error shape? Where do tokens live? Does it need tests, and what kind?

You never answered any of that. So the agent answered it for you, quietly, and you find out which answers you got after it runs.

That is the real failure, and it is worth naming precisely. The code is usually fine. The decisions were never yours.

![画像](https://pbs.twimg.com/media/HOT5wINXQAAB9--?format=jpg&name=large)

Vibe coding loops until it feels right. Spec-driven development runs one approved path from spec to code.

# 2 - What a spec actually is

Same model. Same agent writing the code and running the tests. The only thing that changes is what you hand it first.

A spec is a short document stating your intent before anything gets built. It names what you are building, the constraints you are imposing, what is explicitly out of scope, and the decisions you have already made. It is a contract for how the code should behave.

Notice what is missing from that list. A spec is not there to persuade anyone. It is an execution plan the agent works through.

That single reframe is the whole discipline. You stop prompting an implementation and start describing a system. The ambiguity the agent used to fill with guesses is gone, because you filled it yourself, on paper, where editing costs nothing.

# 3 - The three documents everyone confuses

Most of the confusion around this topic is vocabulary. PRD, design doc, spec, requirements. People use all four to mean all four things. Knowing which one you are writing saves a lot of arguing.

**The PRD is written for people.** Product managers, stakeholders, whoever signs off. It answers what we are building and why it is worth doing. Business value lives here.

**The design doc is written for engineers.** It answers how. Architecture, scaling, security, the trade-offs someone will raise in review.

**The spec is written for the agent.** This is the new one, and it is what this article is about. It borrows a little why from the PRD, because context helps an agent make better calls when it does hit something undefined. But it exists to be executed, not discussed.

Write a spec like a PRD and the agent drowns in business justification it cannot act on. Write it like a design doc and you get architecture with no acceptance criteria. It is its own document, with its own job.

# 4 - The five blocks every spec needs

A working spec has five sections. Miss one and you will feel exactly which one you missed.

**Why.** Two or three sentences of context. What problem this solves and who it is for. This is the block that lets an agent make a sensible call on something you forgot to specify.

**What.** The observable behaviour. Endpoints, inputs, outputs, states. Concrete enough that two engineers reading it would build the same thing.

**Constraints.** The most underrated block, and the one that separates a spec from a wish. Coding agents are eager. Left alone they will install a package, invent a pattern, and refactor a file you never mentioned. Constraints are where you say no in advance.

**Out of scope.** A separate list on purpose. Constraints say how to build it. Out of scope says what not to touch at all. Every feature has an obvious adjacent feature the agent will helpfully add if you do not rule it out.

**Tasks.** The work split into discrete pieces. Each one names what to build, which files it touches, and how you will know it is done.

![画像](https://pbs.twimg.com/media/HOT6FT1XQAA0Y3Y?format=jpg&name=large)

The spec is the primary artifact. Implementation, tests, and docs all trace back to it.

# 5 - A real spec, start to finish

Here is that same authentication request, written properly. Nothing is built yet. This is the entire planning phase.

```text
WHY
Users currently share one account. We need individual
accounts to track usage per user for billing, and to
enable per-user settings.

WHAT
- POST /auth/register, /auth/login, /auth/refresh
- JWT access tokens, 1 hour expiry
- Refresh tokens, 7 day expiry
- Errors return { error, code } with the HTTP status

CONSTRAINTS
- Store users in Postgres via Prisma
- Do not add new dependencies
- Do not store access tokens in the database
- Hash passwords with the library already in the project

OUT OF SCOPE
- Password reset
- Social login
- Email verification
- Rate limiting

TASKS
1. User model + migration      -> prisma/schema.prisma
   Done when: migration runs clean on a fresh database
2. Register endpoint           -> src/auth/register.ts
   Done when: a duplicate email returns 409
3. Login + token issue         -> src/auth/login.ts
   Done when: valid credentials return 200 and a token
4. Refresh flow                -> src/auth/refresh.ts
   Done when: an expired access token refreshes cleanly
```

Read the constraints block again. Four lines, and every one closes a decision the agent would otherwise have made for you. That is the block people skip, and it is the block doing the most work.

Look at the task list too. Every task names a file and a finish condition. Not "build auth." A thing to build, a place to put it, and a test that settles whether it is done.

# 6 - The loop that turns a spec into shipped code

The spec is not the workflow. This is.

**Generate.** Describe the feature and have the agent draft the spec. Writing the first version by hand is slower and no better.

**Review.** This is where your judgement earns its keep. Read it as an adversary. Anywhere the agent still has room to guess, close it now. Every ambiguity you leave here becomes a rewrite later.

**Break down.** Split the work into tasks small enough to finish and verify in one sitting.

**Run one task.** One. Not the whole spec. This is the step people get wrong most often.

**Review and commit.** Check the work against the finish condition you already wrote, commit, move to the next task.

You would never hand a new engineer a forty-page feature document and walk away. You would give them one ticket, review it, then give them the next. An agent is no different, and it fails the same way when you skip that.

The compounding benefit shows up on the second session. Your spec is still there. The agent picks up task four with the same constraints it had for task one, and nothing drifts.

This is the change I keep hitting in my own agent work. The gains stopped coming from a cleverer prompt a long time ago. They come from a tighter spec now, and the difference is not close.

![画像](https://pbs.twimg.com/media/HOT_knQWYAAtJve?format=jpg&name=large)

The first three steps run once. The last two run per task, until the spec is done.

# 7 - When writing a spec is a waste of time

As tradition goes for my articles, let's be honest about who this is not for.

A spec buys predictability. It does not buy speed, and on small work it costs you both.

Skip the spec when:

- The task is one file and one function. Writing the spec takes longer than the fix.
- You are still exploring. If you do not know what you want yet, a spec locks in a guess and makes it expensive to abandon.
- It is throwaway. A prototype you are deleting on Friday does not need a contract.
- You genuinely do not care which approach it picks. If any reasonable implementation works, let it pick one.

The test is simple. Ask whether you would be annoyed if the agent made a particular decision differently. If yes, that decision belongs in a spec. If you shrug, you are fine vibing it.

The trap on the other side is real too, and it is the one I see people walk into after they get religious about this. A spec so detailed it becomes the implementation is just code written in prose, and now you maintain it twice. If your spec is longer than the feature, you did not write a spec. You wrote the thing.

# The spec is the artifact now

For years the spec lived in your head and the code was the thing you shipped. Spec-driven development swaps them. The spec is what you write and approve. The code is what the agent builds from it.

That changes what your job is. You stop being the person typing and become the person deciding. What gets built, what does not, and where the boundaries sit. The agent handles the how.

The engineers who can write a clean spec are about to out-ship the ones still nudging a prompt until it feels right.

Vibe the demo. Spec the thing you actually ship.

**Follow me** [@jeyxbt](https://x.com/@jeyxbt) for more AI builds, workflows, and systems that actually ship. No fluff. Just what works.

![画像](https://pbs.twimg.com/media/HOUDUPzXsAA5Cvf?format=png&name=large)