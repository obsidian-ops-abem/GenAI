---
title: "I Replaced a $2,000 Assistant With a $20 Second Brain"
source: "https://x.com/imryven/status/2082760310792798495"
author:
  - "[[@imryven]]"
published: 2026-07-30
created: 2026-08-04
description: "I didn't hire an assistant this year. I built a second brain instead.Here's what that actually means.A part-time assistant costs $2,000 a mo..."
tags:
  - "clippings"
---
![画像](https://pbs.twimg.com/media/HOdzAqkWoAA4Q3V?format=jpg&name=large)

I didn't hire an assistant this year. I built a second brain instead.

Here's what that actually means.

A part-time assistant costs $2,000 a month. Full-time is $4,000 to $6,000. And even then they don't know your context on day one. You onboard them. You explain your projects. You repeat yourself every time something changes. You manage them. You wait for them. Claude costs $20 a month.

But here's the part most people miss. Claude without a second brain is just a smarter search engine. You ask. It answers. You close the tab. Tomorrow it remembers nothing. You are still the one holding all the context. You are still the one who has to re-explain everything from scratch.

Claude with a second brain is something different entirely. It already knows who you are before you type a word. Your projects. Your decisions. Your thinking style. What you've already tried. What didn't work. Where you're headed. Every session starts where the last one ended.

Here is exactly what it replaced and how.

## Research and analysis

Drop a source into the raw folder. Paste this:

> I just added a new file to my raw folder called \[filename\]. Read it, extract the key concepts and claims, write a wiki article for each major concept, link them to related pages already in my wiki, and flag any contradictions with what I already have. Then summarize what changed in three sentences.

Claude reads it, extracts what matters, writes it into the wiki as a structured article, and links it to everything already there. One source can touch ten to fifteen pages across your vault. Ask a question and it synthesizes across everything you've ever given it. Not just this session. Every session. Every source you've dropped in since day one.

Before this, every research task started the same way. Open Claude. Explain the context. Paste the documents. Ask the question. Get an answer. Close the tab. Next session, repeat everything from scratch. With a second brain, the research doesn't disappear. It compounds.

## Writing and content

Every piece of writing Claude produces for you sounds like you. Not like a polished corporate assistant. Not like AI. Like you. Because before it writes anything, it reads a file called writing-style.md, a detailed analysis of your sentence rhythm, your vocabulary, what you avoid, how you open paragraphs. It writes like you because it has studied you.

Set it up once with this prompt:

> Here are three examples of my writing: \[paste sample 1\] \[paste sample 2\] \[paste sample 3\]. Analyze my writing style in detail. Look at sentence length, rhythm, vocabulary choices, how I open and close paragraphs, what I avoid, how formal or informal I am, and any patterns that make my writing distinct. Save this analysis as a file called writing-style.md in my wiki folder. From now on, whenever I ask you to write anything, read that file first and match my style exactly. Do not default to your own patterns.

Before this, every piece of writing started with a brief. Describe your voice. Describe your audience. Paste examples. Hope it gets close. Now it already knows. One setup. Every piece of writing that follows sounds like you.

## Project management

Two people. Same Claude. Same task. One opens a new chat, spends fifteen minutes explaining the project, where it stands, what's been tried. Gets an answer. Closes the tab. Next session, starts over. The other types one line and Claude already knows the brief.

The difference is one prompt:

> Create a project folder called \[project name\]. Inside it, create four folders: Inputs, Process, Outputs, Feedback. Then write a CLAUDE.md inside that project folder. In it, describe what this project is, its single goal, what done looks like, and your specific role in helping me reach it. Interview me if you need details before writing.

Every project has its own folder. Claude sees only what's relevant to that project, not your entire life, just the one thing. Every decision is linked to the reasoning behind it. Every task that stalled is flagged. Nothing gets lost between sessions.

## Memory that doesn't reset

This is the one that changes everything. Before this, every insight you had in a Claude session disappeared the moment you closed the tab. Every contradiction Claude caught, forgotten. Every pattern it noticed, gone. You were the only persistent layer in the system. And human memory is a terrible place to store operational knowledge.

With a second brain, every answer Claude gives gets filed back into the wiki as a new page. Explorations compound. Contradictions get flagged with dates. Patterns accumulate. At 7am every morning, Claude walks through the entire vault on its own. Links new notes to old ones. Flags what's gone stale. Sends you three lines: what changed overnight, what needs attention, what the system flagged as inconsistent.

Use this prompt to set up the morning briefing:

> Set up a daily scheduled task at 7:00am. Every morning, check my vault. File anything new sitting in Inputs folders into the right place and link it to related notes. Flag anything that has gone stale or hasn't been updated in more than two weeks. Check for contradictions between recent additions and existing wiki pages. Then write me a 3-line summary: what changed overnight, what needs my attention today, and what the system flagged as inconsistent.

You open your laptop and pick up where the thinking already left off. One setup. Every morning starts with a brain that already worked while you slept.

## The setup that makes all of this work

Three folders. One file. That's the entire architecture.

- **raw/** holds everything first. Articles. Transcripts. PDFs. Voice memos. Screenshots. Anything. Unstructured. Unedited. Drop it in and never touch it again. Ground truth.
- **wiki/** is where Claude converts everything in raw into structured articles. Clean. Linked. Organized. Cross-referenced. This is the folder Claude actually thinks from. The human reads it. The model writes it.
- **output/** is where finished work lands. Presentations. Reports. Documents. Anything Claude builds for you using the wiki as its source.

To build this structure, open Claude Code in your vault folder and paste this:

> Create three folders in this directory: raw, wiki, and output. Then create a file called CLAUDE.md at the root. In that file, write instructions for yourself: raw holds immutable source documents that are never edited after arrival. Wiki holds model-generated articles compiled from raw sources, updated every time a new source is added. Output holds finished deliverables built from the wiki. Every session starts by reading this file.

And one file at the root of everything: CLAUDE.md. Not a prompt. A complete picture of a person. Who you are. How you think. What you're building. Where you get stuck. How you want Claude to talk to you. To build yours, paste this into Claude:

> You are setting up my second brain. Interview me one question at a time. Ask about: who I am and what I do, my goals for this year, how I want you to communicate with me, my strengths and weaknesses, and my current projects. Wait for each answer before moving to the next. When finished, write everything into a file called CLAUDE.md at the vault root, organized with clear headers, so you load it automatically every session.

Answer like you are briefing someone who will work with you every single day. When it finishes, you never explain yourself again.

## What this replaces

Think about last week. How many hours did you spend rebuilding context that you'd already built before? Explaining your projects to Claude from scratch. Searching for something you know you wrote down somewhere. Reformatting the same report you formatted last month. Briefing a tool that forgot everything the moment you closed the tab. Multiply that by 52. That's what a second brain gives back. Not in theory. Every week. Compounding.

The people who build this in January are operating on a completely different level by December. Not because they're smarter. Because they stopped resetting and started compounding. Cost: $20 a month. One evening to build.

## What happens over time

- One month in, you stop losing context between sessions. You open Claude and it already knows where you left off. The briefing you used to spend fifteen minutes rebuilding takes zero seconds.
- Three months in, something shifts. You search for something you half-remember thinking about six weeks ago. The vault surfaces it, connected to three other ideas you didn't know were related. You didn't file it consciously. Claude did. You sit there for a moment and realize the system knows things you had forgotten you knew.
- Six months in, the gap between you and someone starting from zero every day is impossible to close. Not because you're smarter. Because your system is compounding and theirs is resetting. Every session they rebuild. Every session you continue.
- One year in, you open the graph view and look at the map of everything you've thought about, decided, researched, and built. Hundreds of nodes. All connected. All yours. It looks like a second mind because it is one. You will never go back to a blank chat box again.

## The honest part

The assistant you almost hired would have needed two weeks to understand your business. The second brain understood it by the end of the first evening. And unlike an assistant, it doesn't forget. It doesn't get tired. It doesn't need to be onboarded again when you start a new project. It doesn't cost more when you give it more work. It doesn't take sick days. It doesn't quit when you find a better model. You just point the new one at the same folder and keep going.

It gets better every single day you use it. You're not buying a tool. You're buying a system that compounds.

Full guide on how to build this in one evening, link below. Bookmark this.

[https://x.com/imryven/status/2079495661930643917](https://x.com/imryven/status/2079495661930643917)