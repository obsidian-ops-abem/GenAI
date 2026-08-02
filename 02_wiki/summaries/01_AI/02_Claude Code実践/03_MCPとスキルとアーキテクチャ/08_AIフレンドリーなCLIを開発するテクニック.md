---
title: "08_AIフレンドリーなCLIを開発するテクニック"
tags: [summary, ai, claude-code, cli, oss, agent-skill, documentation, ai-friendly]
source: https://zenn.dev/shunsuke_suzuki/articles/make-cli-ai-friendly
author: shunsuke_suzuki
published: 2026-08-01
created: 2026-08-02
---

# AI フレンドリーな CLI を開発するテクニック

> **「AI に開発させるのが当たり前になってきた現在では、CLI を開発する際、如何に AI にその CLI に関する知識を持たせるかが重要」** — Coding Agent が Web Fetch せずに CLI のドキュメントにアクセスできる設計。help/ログの agent 向けメッセージ・docs コマンド・Auth の責務分離・ドキュメントと skill の共通化。

出典: [[55_AIフレンドリーなCLIを開発するテクニック（出典）]]（shunsuke_suzuki, Zenn, 2026-08-01。本文はユーザー提供クリップから再構成）

---

## 一行で

OSS CLI を AI（Claude Code/Codex 等）が扱いやすくするための設計論。**AI は知られた OSS でないと知識を持たず、闇雲な Web Fetch は効率が悪く要約で情報が落ちる**。これを解決するため、(1) help/ログに agent 向けメッセージ (2) docs コマンドで Web Fetch 不要 (3) Auth の人間と agent の責務分離 (4) ドキュメントと Agent Skill の共通化、を行う。ghtkn v0.3.5 の実践に基づくが内容は一般論。

## 核心: CLI に AI 向けの知識アクセス経路を埋め込む

AI が CLI を扱う際の問題:
- 知られた OSS でないと AI は CLI の知識を持たない
- Web Fetch は**闇雲で効率が悪く、コンテンツが要約されて情報が落ちる**

解決策は CLI 側に AI 向けの経路を組み込むこと。AI が自律的に正しい知識にアクセスできるようにする。

## 5つの工夫

| # | 工夫 | 内容 |
|---|---|---|
| 1 | **help/ログに agent 向けメッセージ** | docs コマンドへの導線・人間実行が必要な操作の案内・secret 漏洩の注意。人間なら無視する詳細ログも agent は読む |
| 2 | **ドキュメントの編成** | 同一リポジトリで管理・topic ごと分割・YAML Frontmatter の description（Agent Skill の description と同じように agent 向けに記述）・人間と skill の共通化 |
| 3 | **docs コマンドの提供** | `docs list` / `docs show <name>` で Web Fetch 不要。agent が自律的に必要情報を取得 |
| 4 | **Auth の責務分離** | インタラクティブ（Device Flow）は人間、非インタラクティブな操作は agent。credential helper で git 統合・生 token を露出させない |
| 5 | **構造化出力** | `--format json` 等で agent が解析しやすく |

## 実践例（ghtkn）

agent 向けメッセージの3パターン:
1. **docs への導線**: "If you are a coding agent, run 'ghtkn docs list'..."
2. **人間への委譲**: "do NOT run `ghtkn get` yourself; instead, ask the user to run `ghtkn auth`..."
3. **secret 注意喚起**: "Do not print, echo, log... use credential helper or `GH_TOKEN=$(ghtkn get) gh issue list`"

## ドキュメント＝Agent Skill 共通化の思想

> 人間向けのドキュメントと skill を別々に管理するのではなく、**共通化してメンテナンスを容易に**。ドキュメントの本文は人間と agent 両方が読みやすいように書く。

---

## 本ボルト内の位置付け

**Agent 時代の CLI/ドキュメント設計作法**。本ボルトの Claude Code 実践群を補完する「AI への知識提供」設計論:

- **docs コマンドで Web Fetch 不要** は、[[12_カーパシーのObsidian活用術 30分で第二の脳]]（AI がローカルフォルダを直接読む・Web Fetch でなく）と同じ思想。CLI 版の「AI が直接アクセスできる経路」
- **ドキュメント＝Agent Skill 共通化** は、[[01_Agent Skillsを作る完全プロンプト]]（SKILL.md の description・トリガー）と [[10_agent-skill-creator ワークフローをAIスキル化]]（OSS でスキル化）の実践版。**人間向けドキュメントの description をそのまま skill の description に使う**という統合アプローチ
- **help/ログに agent 向けメッセージ** は、[[14_Claudeは多層実行エンジン Chatでない]]（Skills + MCP + CLAUDE.md の多層）を CLI という別のインターフェースで実現。CLI の help/ログが CLAUDE.md の役割を担う
- **Auth の責務分離**（インタラクティブ＝人間、非インタラクティブ＝agent）は、[[03_AI協業の発注の型 HITL実務]]（人が触るのは依頼・承認・検証の3点）の CLI 版。不可逆・インタラクティブな操作は人間へ
- **構造化出力（--format json）** は、[[12_Graph Engineering with Claude 14-Step roadmap]]（node 契約・JSON schema で validate）や [[14_自己レビューエージェントのGraph設計 Anthropicメソッド]]（judge・境界ある入出力）の「契約」思想を CLI 出力に適用
- **「AI に知識を持たせる」** は、本ボルト運用そのもの（CLAUDE.md で AI に運用ルールを与える・raw-sources を直接読ませる）。本記事は CLI 一般にこの思想を拡張

## 関連

- AI が直接アクセス（Web Fetch でない） → [[12_カーパシーのObsidian活用術 30分で第二の脳]]
- Agent Skill の description・トリガー → [[01_Agent Skillsを作る完全プロンプト]]・[[10_agent-skill-creator ワークフローをAIスキル化]]
- 人間の関与ポイント（インタラクティブ＝人間） → [[03_AI協業の発注の型 HITL実務]]
- 構造化出力・契約（schema） → [[12_Graph Engineering with Claude 14-Step roadmap]]・[[14_自己レビューエージェントのGraph設計 Anthropicメソッド]]
- 多層（CLAUDE.md 相当を CLI で） → [[14_Claudeは多層実行エンジン Chatでない]]
