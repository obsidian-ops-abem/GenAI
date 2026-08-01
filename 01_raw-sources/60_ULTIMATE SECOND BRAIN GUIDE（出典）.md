---
title: "60_ULTIMATE SECOND BRAIN GUIDE（出典）"
tags: [raw-source]
source: https://x.com/0xMiraqle/status/2083276253550043271
author: Miraqle (@0xMiraqle)
published: 2026-07-03
created: 2026-08-02
---

# 出典メタデータ

- URL: https://x.com/0xMiraqle/status/2083276253550043271
- 著者: Miraqle（@0xMiraqle）
- 公開: 2026年7月3日
- 形態: X 長文ポスト（ガイド）
- タイトル: **THE ULTIMATE SECOND BRAIN GUIDE + BONUS (that never forgets anything)**

要約は [[05_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]] を参照。

> [!note] 本文はクリップ全文を使用。Karpathy の LLM Wiki gist（2026-04）+ 日本のバズボルト（2026-07、194K views）+ 4層構造 + nightly compiler を統合したガイド。1M コンテキストウィンドウが retrieval pipeline を不要化するという2026年の変化を強調。

---

# 原文（@0xMiraqle の X ポスト全文）

Andrej Karpathy posted a three-layer spec for LLM knowledge bases in April, the gist collected over 5,000 stars in weeks, and he added one line the entire internet underlined: there's room for an incredible new product in the second brain space. Then fifty thousand likes worth of Claude Code and Obsidian posts happened in a single week, a Japanese builder's vault walkthrough pulled 194,000 views in July, and the pattern behind all of it is the same one this guide builds: a brain that captures without friction, compiles itself at night, and briefs you in the morning. Not an app, but - staff.

https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f

## #01: Your second brain died because you were the engine

Be honest about the last one. You built the folders, you tagged the notes, you drew the links, and six months later it was a beautiful graveyard you stopped opening. A widely shared essay this June said the quiet part out loud: the AI does not care about your backlinks. A vault with 4,000 perfectly organized atomic notes still produces generic answers, because organization was never the bottleneck. Retrieval was, and retrieval by hand means you are the search engine, the librarian and the janitor of your own memory. Every system that makes you the engine dies the day you get busy.

> 4月3日
> LLM知識ベース

The 2026 version inverts one thing: the human captures and decides, the agent does literally everything else. Reading, filing, linking, compressing, reminding, briefing. That inversion only became possible this year, and the next section is why.

## #02: What changed in 2026 (the part nobody assembles)

Four unlocks landed within months of each other. Claude memory went free for every user in March, auto-distilling conversations into persistent memory about you. Claude Code shipped Auto Memory on by default, a plain-markdown memory directory the agent maintains per project without being asked. Scheduled cloud agents arrived in spring, cron jobs that think, running on Anthropic's infrastructure with your laptop closed. And Opus 5 landed in July with a 1M token context window as the default, at the same $5 price as the model before it, which means a full mid-size vault now fits in one request with no retrieval pipeline pretending to be memory.

Video: https://www.youtube.com/watch?v=PupmfSttxlc

ChatGPT moved the same direction, Dreaming V3 rewrites stale memories in the background and OpenAI claims recall jumped from 41.5 to 82.8 percent. The direction of the whole industry is one sentence: memory stopped being your job.

## #03: The architecture: four layers, one writer each

The viral Japanese build got one thing exactly right, and it is the architectural spine of this guide: separate the vault into four layers and give each layer exactly one writer.

```markdown
/vault
  /0-raw        # capture inbox. YOU write here, nothing else does
  /1-wiki       # compiled knowledge. only the NIGHTLY AGENT writes here
  /2-digest     # briefs, summaries, reviews. only SCHEDULED JOBS write here
  identity.md   # who you are, how you decide. only YOU edit, agent reads first
  projects.md   # live state of what you're building
  tasks.md      # open loops
```

Raw is allowed to be chaos, that is its job. The wiki is written by the agent compiling raw into clean pages, the digest layer is where briefs land, and identity is the file that makes every answer sound like your assistant instead of a stock chatbot.

> One writer per layer kills the failure mode where notes overwrite each other and nobody trusts the vault anymore. The whole thing is plain markdown in a folder, which matters more than any feature: the moment an AI can read and write your notes natively you have a second brain, the moment it can't you have a pretty prison.

An honest aside before you build: if you don't want to run any of this, NotebookLM or a Claude Project over your files gets you 80 percent of the value for zero setup. This guide is for the other 20 percent, which happens to be the part that compounds.

> 7月5日
> 海外AI勢が書いていた「Fable 5で第二の脳を作る方法」という記事がかなり参考になった。 要点は、AIの性能差は「モデルの賢さ」だけでなく、どれだけ自分の知識・過去の意思決定・業務文脈を読ませられるかで決まるという話。 Fable

## #04: Capture: if it takes more than one tap, you won't do it

The essay crowd is right about one more thing: the bottleneck moved from organization to capture. The AI cannot read what you never captured, and you will not capture anything that takes three taps at a red light. So the entire capture design is one rule, one inbox, zero decisions in the moment.

On the phone that means voice. An iOS shortcut on the Action Button that appends dictation to a raw note, or a Voice Memos pipeline where the agent transcribes, cleans and files recordings into the vault on its own. On the desktop it's one hotkey appending to the same inbox. You never sort, never title, never tag at capture time, you say the thought and go back to your life. The nightly compiler exists precisely so this stays messy.

https://drew.tech/posts/ios-memos-obsidian-claude

## #05: Connect the agent to the folder

Everything before this step is a well-organized folder. The step itself is one command: open a terminal in the vault and run claude.

The agent now reads and writes every file natively, and Auto Memory means it keeps its own notes about your project without being told. If you live in Obsidian's interface, half a dozen MCP servers wire the same vault into Claude Desktop, and local-first memory servers like Basic Memory run the same trick across Claude, Codex and Cursor at once, the registry lists 139 memory servers alone. Pick one door, the files are the same behind all of them.

https://mindwiredai.com/2026/07/17/build-a-second-brain/

Then pin the one rule that makes retrieval automatic, a short file the agent must read first, every run:

```markdown
# CONTEXT RULE (read first, every time)
1. Read identity.md and projects.md before answering anything.
2. Pull ONLY the notes that match the question, follow their links
   one hop out, and cite every note you used by name.
3. If the vault has the answer, never answer from training data.
4. If the vault does not have it, say so plainly.
5. When I say "remember this", write it to the right layer yourself.
```

## #06: The nightly compiler (the feature every dead vault was missing)

Here is the line to tattoo somewhere: compiled knowledge rots if left alone. The builder behind the viral vault runs a compiler at 23:30 every night, and the open-source flagship in this space, a 3.7K-star repo, ships four scheduled agents out of the box: morning brief, nightly consolidation, weekly review, vault health. That is the entire difference between this generation and yours that died. The maintenance you always skipped is now a job that runs while you sleep.

https://github.com/eugeniughelbur/obsidian-second-brain

```markdown
NIGHTLY (runs at 03:00):
Read everything new in /0-raw. File each item: facts about me into
identity.md, tasks into tasks.md, project updates into projects.md,
knowledge into the right /1-wiki page, rewriting the page so it stays
one clean current version instead of a pile of appendices. Find links
between this week's notes and older ones and note them in the wiki.
Flag anything untouched for 14+ days. Then write tomorrow's brief
to /2-digest/brief.md: project state, open loops, what carried over,
one thing I'm avoiding that matters. Log every decision.
```

Wire it as a local loop if the machine stays on, or as a scheduled cloud agent so it runs with the laptop shut. Add a Sunday pass that hunts contradictions and decayed pages. The vault stops rotting the week this job exists.

## #07: The morning brief is the product

Every layer above exists so that one page can exist: you open the laptop and the brief is already there. Current state of every project, open loops with the stale ones flagged, yesterday's captures already filed, connections you didn't consciously make, and the one uncomfortable item the notes keep circling. Thirty seconds of reading that replaces the forty minutes of "where was I". Ask anything deeper and the agent answers from your own history, citing the notes, because the context rule forbids it from guessing.

https://platform.claude.com/docs/en/managed-agents/scheduled-deployments

And the 1M window changes what "deeper" means: the whole vault, months of it, fits in a single conversation. Point a run at your entire archive and ask what you were wrong about this quarter. That question was science fiction in January.

## #08: The honest section: how these die now

New failure modes replaced the old ones, and pretending otherwise is how you build graveyard number four. Past roughly two thousand notes, dumping everything into context degrades, which is what the wiki layer is for, compiled pages instead of raw sprawl.

Agents hallucinate synthesis, which is why the context rule demands citations and the compiler logs decisions. Schemas drift silently, which is the Sunday health pass. And a cloud agent reading your whole life is a privacy decision you should make once, deliberately, keep the vault local, keep secrets out of it, and gate anything irreversible. One hour a week of being the quality gate keeps the other 167 honest.

## #09: Build it this weekend. The real insight.

Day one: make the folder, the four layers, seed identity.md and projects.md properly, wire the one-tap capture. Day two: connect the agent, pin the context rule, run the nightly job by hand once to watch it file your mess, then schedule it. That's the whole build, a folder, five files, one command and two habits.

https://mcpservers.org/category/memory

The real insight is that the second brain space spent a decade optimizing the wrong half. Prettier graphs, cleverer tagging, another plugin, all of it sharpening storage while retrieval and maintenance stayed your job, and you were always going to quit that job. The moment the maintenance became an agent's job, the oldest promise of the field, a brain that knows you and gets smarter every day, quietly became a weekend project. Most people will read the wave, save a thread and keep their graveyard. The handful who give the janitor keys to the library wake up every morning slightly ahead of who they were yesterday.
