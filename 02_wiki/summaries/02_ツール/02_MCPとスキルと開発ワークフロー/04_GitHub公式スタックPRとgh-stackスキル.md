---
title: "04_GitHub公式スタックPRとgh-stackスキル"
tags: [summary, tool, github, stacked-pr, agent-skill, claude-code, codex, workflow]
source: https://x.com/tonkotsuboy_com/status/2083462188640170237
author: tonkotsuboy_com (@tonkotsuboy_com)
published: 2026-08-01
created: 2026-08-01
---

# GitHub 公式スタック PR と gh-stack スキル

> **「GitHub公式のスタック型 PR を、gh-stack スキルで自然言語で操れる」** — 変更を依存関係のある小さな PR の連鎖として積み重ね、Claude Code/Codex から自然言語で操作する Agent Skill。

出典: [[53_GitHub公式スタックPRとgh-stackスキル（出典）]]（tonkotsuboy_com @tonkotsuboy_com, 2026-08-01。本文はユーザー提供クリップから再構成。※図解4枚のテキスト要約、詳細は解説記事参照）

---

## 一行で

**GitHub 公式のスタック型プルリクエスト（Stacked PR）**＝ 変更を依存関係のある一連の小さな PR として積み重ねる機能。巨大 PR を分割レビュー可能にし、**gh-stack** という Claude Code/Codex 用 Agent Skill を使えば**自然言語でスタック PR を作成・管理**できる。

## 解く問題

大きな機能を1つの巨大 PR で出すと:
- レビュー負荷が高い
- 依存する変更が混在し、一部だけマージできない
- コンフリクト解消が一度に集中する

スタック PR は変更を **PR1 → PR2 → PR3 ... の依存連鎖**に分割し、各々を小さく保ちつつ順にマージできる。

## 核心: GitHub 公式機能を自然言語で操る

GitHub 公式のスタック PR を直接コマンドで操作するのでなく、**gh-stack Agent Skill** を使えば「この変更を3つのスタック PR に分けて」等の**自然言語で操作**できる。解説: [Zenn — gh-stack introduction](https://zenn.dev/ubie_dev/articles/gh-stack-introduction)

---

## 本ボルト内の位置付け

**エージェント時代の PR ワークフロー**。02_ツール カテゴリだが、Agent Skill の具体適用例としても位置付けられる:

- **Agent Skill の実例**: [[01_Agent Skillsを作る完全プロンプト]]（手書きプロンプト）・[[01_agent-skill-creator ワークフローをAIスキル化]]（OSS 自動生成）が「Skill をどう作るか」なら、gh-stack は **Skill で定型的 Git/PR 操作を自然言語化した実例**。[[04_Claudeは多層実行エンジン Chatでない]]（Skills + MCP が実行エンジンのアンロック）の具体事例
- **「自然言語で操作」** は [[04_カーパシーのObsidian活用術 30分で第二の脳]]（日本語で頼むだけ・プログラム書けないでも OK）と同思想。gh-stack は Git の複雑なコマンドを自然言語で Wrap
- **変更の分割・依存連鎖** は、[[03_Graph Engineering with Claude 14-Step roadmap]]（node 契約・edge は依存）や [[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]（バッチ・state ディスク）の「依存関係の明示化」を PR 単位で実現。スタック PR の依存連鎖 = グラフの edge
- **小さく保つレビュー** は [[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]（judge・ルールブック・バッチ再生成）の「小さな単位で検証」思想の PR 版。巨大な一括変更でなく段階的検証

## 関連

- Agent Skill の作法 → [[01_Agent Skillsを作る完全プロンプト]]・[[01_agent-skill-creator ワークフローをAIスキル化]]
- Skills + MCP（自然言語で実行） → [[04_Claudeは多層実行エンジン Chatでない]]
- 日本語で頼むだけ → [[04_カーパシーのObsidian活用術 30分で第二の脳]]
- 依存関係の明示化（グラフの edge） → [[03_Graph Engineering with Claude 14-Step roadmap]]
- 小さな単位で検証 → [[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]
