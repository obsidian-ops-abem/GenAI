---
title: "64_openai codex-plugin-cc Claude CodeからCodexへ委譲（出典）"
tags: [raw-source]
source: https://github.com/openai/codex-plugin-cc
author: OpenAI
published:
created: 2026-08-02
---

# 出典メタデータ

- URL: https://github.com/openai/codex-plugin-cc
- 作者: OpenAI（公式）
- 形態: GitHub リポジトリ（Claude Code プラグイン）
- タイトル: **codex-plugin-cc** — Use Codex from Claude Code to review code or delegate tasks

要約は [[05_openai codex-plugin-cc Claude CodeからCodexへ委譲]] を参照。

---

# 原文（README 要点）

## Codex plugin for Claude Code

Use Codex from inside Claude Code for code reviews or to delegate tasks to Codex.

This plugin is for Claude Code users who want an easy way to start using Codex from the workflow they already have.

## What You Get

- `/codex:review` for a normal read-only Codex review
- `/codex:adversarial-review` for a steerable challenge review
- `/codex:rescue`, `/codex:transfer`, `/codex:status`, `/codex:result`, and `/codex:cancel` to delegate work, hand off sessions, and manage background jobs

## Requirements

- **ChatGPT subscription (incl. Free) or OpenAI API key.** Usage contributes to Codex usage limits.
- **Node.js 18.18 or later**

## Install

Add the marketplace in Claude Code:

```
/plugin marketplace add openai/codex-plugin-cc
/plugin install codex@openai-codex
/reload-plugins
/codex:setup
```

`/codex:setup` will tell you whether Codex is ready. If Codex is missing and npm is available, it can offer to install Codex for you. If Codex is installed but not logged in yet, run `!codex login`.

After install: the slash commands listed below + the `codex:codex-rescue` subagent in `/agents`.

## Usage

### /codex:review
Runs a normal Codex review on your current work. Same quality as running `/review` inside Codex directly.
- review of current uncommitted changes
- review of your branch compared to a base branch like `main`
- Use `--base <ref>` for branch review. Supports `--wait` and `--background`.
- **Not steerable** and does not take custom focus text. Use `/codex:adversarial-review` when you want to challenge a specific decision or risk area.
- **Read-only** — will not perform any changes.

### /codex:adversarial-review
Runs a **steerable** review that questions the chosen implementation and design.
- pressure-test assumptions, tradeoffs, failure modes, and whether a different approach would have been safer or simpler
- review focused on design choices, tradeoffs, hidden assumptions, and alternative approaches
- pressure-testing around specific risk areas like auth, data loss, rollback, race conditions, or reliability
- Takes extra focus text after the flags.
- **Read-only. Does not fix code.**

### /codex:rescue
Hands a task to Codex through the `codex:codex-rescue` subagent.
- investigate a bug
- try a fix
- continue a previous Codex task

### その他: /codex:transfer, /codex:status, /codex:result, /codex:cancel
- delegate work, hand off sessions, and manage background jobs
