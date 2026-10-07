---
title: "Opus 5.5 Is 2x the Price on Paper. In a Long Agent Loop It's 1.26x (Full Guide)"
source: "https://x.com/gippp69/status/2106744347836153989"
author:
  - "[[@gippp69]]"
published: 2026-10-04
created: 2026-10-08
description: "Handing a Sonnet 5.5 session to Opus 5.5 at 300K tokens of context costs $1.50. Opus hasn't read a file or written a line yet. That's just r..."
tags:
  - "clippings"
---
![画像](https://pbs.twimg.com/media/HTxwgGKWcAA4F6n?format=jpg&name=large)

Handing a Sonnet 5.5 session to Opus 5.5 at 300K tokens of context costs $1.50. Opus hasn't read a file or written a line yet. That's just rebuilding the cache for the new model.

Meanwhile the thing everyone argues about, "Opus is twice as expensive", stops being true the moment your agent runs long enough.

Same price sheet, two very different bills. The difference is turns.

Below: the math, the break-even rule, a script you can run on your own logs, and the escalation habit that burns more money than picking the wrong model.

TLDR: cache reads cost $0.20 per million on both models, so in long sessions the per-turn gap shrinks from 2x to somewhere between 1.9x and 1.26x. Opus only has to finish in about a third fewer turns to cost the same. And escalate early or with a short handoff, never at 300K.

## 1\. The numbers first

```plaintext
SONNET 5.5      OPUS 5.5
fresh input              $2 / 1M         $4 / 1M
output (incl. thinking)  $10 / 1M        $20 / 1M
cache write, 5 min       $2.50 / 1M      $5 / 1M
cache read               $0.20 / 1M      $0.20 / 1M   <- same
context / max output     1M / 128K       1M / 128K
API effort default       high            medium
batch API                -50%            -50%

per agent turn, my example loop (section 3)
at 20K context           $0.033          $0.062       1.88x
at 150K context          $0.059          $0.088       1.49x
at 400K context          $0.109          $0.138       1.26x

switching to Opus mid-task at 300K       $1.50 just to re-cache
same switch with a 20K handoff           $0.10

one cache miss at 150K (coffee break)    $0.38 Sonnet / $0.75 Opus
1-hour cache pays off after              1 miss per ~45 turns
1,000 tasks a month, switch late         ~$2,530
1,000 tasks a month, hand off early      ~$1,820
```

The top block is from Anthropic's price sheet. The bottom block is my arithmetic on it. The turn shape is an assumption and section 3 shows exactly what it is.

## 2\. Where an agent turn's money actually goes

A chat costs what the price sheet says. An agent loop doesn't, because every turn re-sends the whole conversation.

With prompt caching on, one turn breaks into three parts:

```plaintext
1. READ    the whole conversation so far     -> cache read rate
2. WRITE   what's new since last turn        -> cache write rate
           (tool results, your message, the last answer)
3. OUTPUT  thinking + the visible answer     -> output rate
```

Only parts 2 and 3 are priced 2x on Opus. Part 1 costs the same on both.

And part 1 is the one that grows. Turn 3 reads 20K. Turn 30 reads 300K. The longer the session, the more of each turn is billed at a price where Opus and Sonnet are equal.

So the 2x is real for new tokens and doesn't exist for old ones.

## 3\. The per-turn table

My example turn: 5,500 new tokens written to cache, 1,500 output tokens, and a context that's already sitting in cache.

```plaintext
context in cache     SONNET 5.5     OPUS 5.5     OPUS / SONNET
20K                  $0.033         $0.062       1.88x
150K                 $0.059         $0.088       1.49x
400K                 $0.109         $0.138       1.26x
```

How one row is built, Sonnet at 150K:

```plaintext
read    150,000 x $0.20 / 1M    = $0.0300
write     5,500 x $2.50 / 1M    = $0.0138
output    1,500 x $10   / 1M    = $0.0150
total                             $0.0588
```

Same row on Opus is $0.0300 + $0.0275 + $0.0300 = $0.0875.

Your turn shape will be different. Long sessions push the ratio toward 1x. Heavy output pushes it back toward 2x, because output is where Opus really costs double:

```plaintext
at 150K context        SONNET 5.5     OPUS 5.5     OPUS / SONNET
1,500 output tokens    $0.059         $0.088       1.49x
4,000 output tokens    $0.084         $0.138       1.64x
```

Hidden thinking counts as output. So effort level moves this ratio more than the context does. Section 8 measures yours.

![画像](https://pbs.twimg.com/media/HTterP1WgAAEfFz?format=jpg&name=large)

## 4\. What actually decides it: turns per finished task

Price per turn is half the bill. The other half is how many turns it takes to actually finish.

```plaintext
cost per task = cost per turn x turns to finish

Opus wins when:
  Opus turns  <  Sonnet turns  /  (Opus turn cost / Sonnet turn cost)
```

In plain words, how many fewer turns Opus needs to break even:

```plaintext
context        ratio      Opus must finish in
20K            1.88x      47% fewer turns
150K           1.49x      33% fewer turns
400K           1.26x      21% fewer turns
```

A worked example at an average of 150K context:

```plaintext
Sonnet 5.5   30 turns x $0.0588   = $1.76
Opus 5.5     20 turns x $0.0875   = $1.75
```

Basically a tie. And if Sonnet circles the problem for 40 turns while Opus finishes in 20, Opus is the cheap one by $0.60.

Short tasks are the opposite. At 20K context Opus has to cut turns almost in half. For quick, well-scoped work, Sonnet wins almost every time.

## 5\. The escalation tax

This one costs more than the model choice itself, and almost nobody counts it.

The cache is per model. When you switch a running session from Sonnet to Opus, Opus can't read what Sonnet cached. Everything gets written again, at Opus's write price.

```plaintext
switch at 300K context     300,000 x $5 / 1M   = $1.50
that equals                ~17 Opus turns at 150K
switch with 20K handoff     20,000 x $5 / 1M   = $0.10
```

The worst pattern is the most common one: let Sonnet try for 40 turns, watch it fail, then hand the whole bloated conversation to Opus. You pay for the failed attempt, then pay $1.50 to give Opus all of Sonnet's dead ends to read.

Two habits that fix it:

```plaintext
ESCALATE EARLY      decide on the model in the first few turns,
                    while the context is still small

ESCALATE CLEAN      start Opus in a new session with a short handoff:
                    the task, the failing check, the 3 files that matter.
                    not the transcript.
```

A handoff is also better input. Opus gets the evidence, not 40 turns of Sonnet guessing.

![画像](https://pbs.twimg.com/media/HTtevx9WYAAG9uL?format=jpg&name=large)

## 6\. The coffee-break tax

The default cache lives 5 minutes. Step away longer and the next turn rewrites the whole context from scratch.

```plaintext
one cache miss at 150K
  Sonnet 5.5     150,000 x $2.50 / 1M   = $0.38
  Opus 5.5       150,000 x $5   / 1M    = $0.75
```

The fix is the 1-hour cache. It costs more on every write, so it only makes sense if you actually pause:

```plaintext
1-hour cache, extra per turn (5,500 new tokens)
  Sonnet 5.5     5,500 x ($4 - $2.50) / 1M   = $0.008
  Opus 5.5       5,500 x ($8 - $5)    / 1M   = $0.017

break-even      one avoided miss pays for ~45 turns
```

Simple rule. If your sessions have a gap longer than 5 minutes at least once every 45 turns (you review a diff, take a call, grab a coffee), switch to the 1-hour cache. If the agent runs straight through, don't.

## 7\. What it adds up to

Say you run 1,000 tasks a month. Sonnet 5.5 passes 80% in about 30 turns. The other 20% go to Opus 5.5, which finishes in about 20.

The only difference between the two setups is when and how you hand off:

```plaintext
SWITCH LATE (the common way)
  80% Sonnet passes        30 turns                      $1.76
  20% Sonnet fails         40 turns          $2.35
                           switch at 300K    $1.50
                           Opus 20 turns     $1.75       $5.60
  average per task                                       $2.53
  per month                                             ~$2,530

HAND OFF EARLY
  80% Sonnet passes        30 turns                      $1.76
  20% caught by turn 6     6 turns at 20K    $0.20
                           20K handoff       $0.10
                           Opus 20 turns     $1.75       $2.05
  average per task                                       $1.82
  per month                                             ~$1,820
```

About $710 a month, same models, same pass rate. The whole difference is 34 wasted Sonnet turns and one fat cache write per failed task.

The catch: you have to spot the hard task by turn 6. A check that runs early (does it compile, does the first test pass, did it touch the right files) is what makes that possible.

## 8\. Measure it on your own logs

Every response from the Messages API carries a usage object. Log it per call with a task id, then run this:

```python
import json
from collections import defaultdict

PRICE = {  # $ per 1M tokens, Oct 2026 list prices, 5-minute cache writes
    "claude-sonnet-5-5": {"in": 2.0, "out": 10.0, "read": 0.20, "write": 2.50},
    "claude-opus-5-5":   {"in": 4.0, "out": 20.0, "read": 0.20, "write": 5.00},
}

def call_cost(model, u):
    p = PRICE[model]
    return (u["input_tokens"] * p["in"]
            + u["output_tokens"] * p["out"]
            + u.get("cache_read_input_tokens", 0) * p["read"]
            + u.get("cache_creation_input_tokens", 0) * p["write"]) / 1e6

# usage.jsonl: one line per API call
# {"task": "fix-auth", "model": "claude-sonnet-5-5", "passed": true, "usage": {...}}
tasks = defaultdict(lambda: {"turns": 0, "cost": 0.0, "passed": False, "model": ""})
for line in open("usage.jsonl"):
    r = json.loads(line)
    t = tasks[(r["model"], r["task"])]
    t["turns"] += 1
    t["cost"] += call_cost(r["model"], r["usage"])
    t["passed"] = t["passed"] or r.get("passed", False)
    t["model"] = r["model"]

for model in PRICE:
    rows = [t for t in tasks.values() if t["model"] == model]
    done = [t for t in rows if t["passed"]]
    if not rows:
        continue
    turns = sum(t["turns"] for t in rows) / len(rows)
    per_turn = sum(t["cost"] for t in rows) / sum(t["turns"] for t in rows)
    per_pass = sum(t["cost"] for t in rows) / max(len(done), 1)
    print(f"{model:20} tasks {len(rows):3}  passed {len(done):3}  "
          f"avg turns {turns:5.1f}  $/turn {per_turn:.4f}  $/pass {per_pass:.3f}")
```

Two lines of output, one per model. Then read them in this order:

```plaintext
$/turn ratio       how far from 2x your loop really is
avg turns ratio    does Opus actually finish faster on these tasks
$/pass             what you actually pay per finished task
```

$/pass divides total spend by tasks that passed your check, so failed attempts are counted, not hidden.

## 9\. The decision table

```plaintext
SITUATION                                  START WITH
short task, clear spec, under ~30K         Sonnet 5.5, medium
long session, cross-file, ambiguous        Opus 5.5, medium
Sonnet takes 1.5x+ the turns of Opus       Opus, for that task class
Sonnet fails a real check                  Opus, fresh session + handoff
offline evals, backfills                   either, via batch at -50%
```

These are starting points. Run the script for a week and replace them with your own numbers.

## 10\. The guards

```plaintext
switching models mid-session        -> new session + handoff, never the transcript
cache read stays at 0               -> prefix under 512 tokens or not stable, fix first
changing top-level effort per turn  -> breaks the cache, pick one per session
max_tokens set low to "save"        -> cut-off answer + a second run, not a saving
"Opus feels better"                 -> not a routing rule, $/pass is
```

## 11\. What I haven't tested

The turn shape. 5,500 new tokens and 1,500 output per turn is an example, not a measured average. Heavy tool output or long thinking moves every ratio in this article.

The turn-count gap. I don't have a measured number for how many fewer turns Opus 5.5 needs on real work. That's exactly what the script is for, and it will differ by task type.

Cache misses. Most of the math assumes the cache stays warm. Section 6 shows what a miss costs, but I don't have a measured miss rate for real sessions.

The monthly example. 80% pass rate, 30 vs 20 turns and "caught by turn 6" are assumptions to show the shape of the math, not numbers from my logs.

Thinking. Hidden thinking is billed as output. If one model thinks much more at the same effort level, the output line changes and so does the ratio.

## 12\. The playbook

Price turns, not tokens.

Watch output and thinking. That's where Opus really costs 2x.

Long sessions shrink the gap. Short ones don't.

Opus only needs to finish in about a third fewer turns at 150K to break even.

Decide the model early, while the context is small.

Pausing a lot? Use the 1-hour cache. Running straight through? Don't.

Escalate with a handoff, never with the transcript.

Judge both models by $/pass on your own logs.

## The point

The price sheet compares tokens. Your agent spends turns.

Once most of each turn is a cache read, Opus 5.5 stops being "the 2x model" and becomes a question: does it finish fast enough to pay for itself? Sometimes it does, sometimes it really doesn't.

The expensive mistake isn't picking the wrong model. It's switching at 300K and paying $1.50 to make the new model read the old one's mistakes.

Measure turns. Escalate early. Hand off clean.

Prices are Anthropic's standard API rates as of October 3, 2026. Per-turn figures are my arithmetic on an assumed turn shape, not a measured bill.

If you want more breakdowns like this, I post one every couple of days on Telegram and X. Both free.

X - [https://x.com/gippp69](https://x.com/gippp69) TG - [https://t.me/GipArcAI](https://t.me/GipArcAI)