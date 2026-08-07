---
title: "Graph Engineering: The 11-Step Roadmap From Obsidian Vault to Graph Fable 5 Can Actually Use"
source: "https://x.com/unicodef1wn/status/2080693998180606075"
author:
  - "[[@unicodef1wn]]"
published: 2026-07-25
created: 2026-08-08
description: "Everyone's building a second brain in Obsidian. Almost no one builds the part that makes it work.Open your vault right now. If it's a flat p..."
tags:
  - "clippings"
---
![画像](https://pbs.twimg.com/media/HOAbqYyWYAA8Yc1?format=jpg&name=large)

Everyone's building a second brain in Obsidian. Almost no one builds the part that makes it work.

Open your vault right now. If it's a flat pile of notes with no structure, here's what happens every time you ask Fable 5 one question: **it reads all of them.**

That's 2,000 notes and a few hundred thousand tokens burned to hand you one answer. Slow, expensive, and it still grabs the wrong file half the time.

That's not a second brain. It's a vault full of markdown files you taught Fable 5 to search blind.

A second brain is only as good as the graph underneath it, not the vault. This is the 11-step roadmap from an Obsidian folder Fable 5 drowns in to a structure it walks in seconds.

**The vault** every note read, every single question **The tokens** hundreds of thousands per answer, billed every time **The graph** 2 to 3 files read, answer back in under a second **The delta** a fraction of the cost, right far more often

> Before we go further: follow me on X, [@unicodef1wn](https://x.com/unicodef1wn), and the deeper breakdowns go out first on Telegram, [t.me/unicodef1wn](https://telegram.me/unicodef1wn). That's the only ask in this article. Everything below is the actual system.

Everyone screenshots the wrong layer, Obsidian's graph view, the glowing web of dots. One builder running this at scale put it well:

> "The pretty graph is for you. The index is for Fable 5. Only one of them makes it faster."

The poster on the wall versus the engine under the hood.

![画像](https://pbs.twimg.com/media/HOAWeeoWAAAb1DA?format=jpg&name=large)

Graph engineering means building the engine inside your vault: the router, the index, the nodes, the edges, and the logic that walks them.

**11 steps.** The part that decides whether your Obsidian vault thinks, or just sits there looking impressive.

## Part 1: What a Graph Actually Is

1\. A graph is a routing structure, not a picture

Four parts. A **router** Fable 5 reads first. An **index** it checks against. **Nodes** that hold the knowledge. **Edges** that point between them.

That covers it. Obsidian's graph view proves connections exist. It doesn't stop Fable 5 from reading 2,000 notes to answer one question.

2\. The whole graph lives in one folder

Everything that shapes retrieval sits inside your vault, in one directory. Learn this layout once and you can read anyone's second brain in ten seconds:

vault/ ├─ ROUTER.md # read every session, kept tiny ├─ index.md # one line per note: name, link, description ├─ nodes/ # your notes, one idea per file │ ├─ billing-rules.md │ └─ client-acme.md ├─ routines/ # how you save and how you find └─ state.md # what survives between sessions

You should be able to say why every file exists. If you can't explain a note, it shouldn't be a separate node.

![画像](https://pbs.twimg.com/media/HOAWkrOXgAA3TYB?format=jpg&name=large)

3\. Pile vs. graph vs. system

- **The pile.** An Obsidian vault with no map. Ask Fable 5 anything, it reads everything.
- **The graph.** The same vault with a router, an index, and edges. Ask it anything, it reads two or three files.
- **The system.** A graph with memory that compounds. Every session leaves the next one sharper.

Most "my Obsidian vault is a mess" complaints are these three tangled together. Pull them apart and it gets fast.

4\. The default vault is a hoarder, not a librarian

Point Fable 5 at a raw Obsidian folder and it "works." It finds things by reading the whole vault on every question.

That's fine at 30 notes. At 3,000, every question scans your entire vault and you pay for it.

A hoarder owns everything and finds nothing on command. A librarian owns the same shelf and hands you the right book in five seconds. The next seven steps turn Fable 5 into the librarian.

## Part 2: Build the Graph

5\. The router, kept brutally short

The router is a single note Fable 5 reads at the start of every session. It's a map, not a manual: "billing lives here, this client lives there."

The mistake almost everyone makes: it grows into a novel, and every session drags that novel into context before Fable 5 does anything.

Keep it under 500 tokens. Facts and directions only. The actual knowledge lives in the nodes, not the router.

6\. The index, one line per node

The index is how Fable 5 knows where an answer lives without opening a note. One line per node: name, link, one sentence.

\# index - billing-rules.md | rates, refunds, escalation | how we bill and when - client-acme.md | Acme account | contacts, scope, gotchas - voice.md | writing voice | tone rules + banned words

Every new note gets a line here, automatically, from day one. A current index keeps retrieval cheap for life. Let it drift and your vault turns back into a pile.

7\. Nodes, one idea per file

A node does one job. Small, specific, named so the title alone tells you what's inside.

The enemy is the mega-note, the file that quietly grew to cover ten topics because starting a new one in Obsidian felt like overhead. To use one line of it, Fable 5 has to swallow all ten.

Split by idea. Small nodes are cheap to open. Fat ones cost you every time.

8\. Edges, pointers, not the graph view

An edge is a link inside one note to another, an Obsidian \[\[wikilink\]\] with a purpose. That's the real graph, the pointer, not the visual you'd screenshot from graph view.

A node answers half your question and links to the one that finishes it. Fable 5 follows one edge instead of digging through the whole vault.

Link only what a node actually depends on. Ten sharp edges make a graph. Fifty is just noise wearing a graph's clothes.

9\. Retrieval as logic, not model calls

Here's why most vaults stay slow: they let Fable 5 do the finding. But finding doesn't need intelligence. It's a matching problem, and it shouldn't cost you a model call.

The order that works:

1. Strip the question to keywords, drop the filler
2. Score every node from index.md alone, open nothing
3. Open only the best match, not three "just in case"
4. Read only the section that answers, not the whole note
5. Follow one edge if that section points elsewhere
6. Now Fable 5 runs once, evidence already in hand

![画像](https://pbs.twimg.com/media/HOAWuCbWQAA5H9j?format=jpg&name=large)

Steps one through five cost nothing, because they're logic, not model turns. One real build of this cut cost sharply against the default and answered faster on nearly every question.

## Part 3: Make It Compound

10\. Prove it

Don't take it on faith. Run the same questions through a raw Obsidian vault and through your graph, side by side.

Compare tokens. Compare wall-clock time. Check that the answers are actually right. This is the step most people skip, and the one that turns "I think it's faster" into an actual table you can point to.

11\. Add memory, then ship it

Fable 5 wipes clean between runs. Your vault doesn't have to.

A state file logs what was tried, what worked, what didn't. Every run ends by writing to it and starts by reading it.

The value was never Fable 5. It's the router, the index, the edges, the rules: plain markdown in a vault you own. Swap the model behind it next year and you lose nothing.

## The Graph Mistakes That Keep Your Vault Slow

- **Running on the raw vault.** No router, no index. Fable 5 reads everything to answer anything.
- **A bloated router.** A router note that turned into a manual. Cut it to 500 tokens of pointers.
- **No index.** Without it Fable 5 can't guess where an answer lives, so it opens notes blind.
- **Mega-notes.** One giant file it has to swallow whole to use one line of. Split by idea.
- **Letting the model do the finding.** That's a logic job. Save Fable 5 for the thinking.
- **No memory.** Every run restarts from zero. The state file is what lets tomorrow pick up where you left off.
- **Building the graph before you have notes.** Empty structure is busywork. Write first, then engineer.

## Conclusion

Obsidian's graph view gets the screenshots. The index does the work.

Everyone falls for the galaxy of dots. It's a poster.

Every decision that makes Fable 5 fast or slow, cheap or expensive, sharp or vague lives one floor beneath your vault: the router you wrote, the index you kept honest, the edges you drew, the logic that finds before the model thinks.

Build that floor and everything above it compounds. Fable 5 reads three notes instead of two thousand. The bill drops. The answers get sharper.

Pick the one thing you're not doing, probably the index, or logic before model calls, and add it today. Keep the graph small enough to explain out loud.

**P.S.** If you build one thing off this, make it the index. Not the router, not the edges, the index. It's the cheapest step here and the one every other step scores against. Get that right and the rest gets fast almost on its own.

Bookmark this before it gets buried. If it helped, send it to one person still dragging Fable 5 through a raw Obsidian vault.

**More of this, before the rest of the feed buries it too: follow on X at** [@unicodef1wn](https://x.com/unicodef1wn)**, and get the next breakdown early on Telegram at** [t.me/unicodef1wn](https://telegram.me/unicodef1wn)**.**