---
title: "04_Claudeは多層実行エンジン Chatでない"
tags: [summary, ai, claude-code, execution-engine, skills, mcp, claude-md, layers]
source: https://x.com/HeyAnjula/status/2083154660198945096
author: HeyAnjula (@HeyAnjula)
published: 2026-07-31
created: 2026-08-01
---

# Claude は多層実行エンジン —— Chat ではない

> **「Most people are using Claude like a chatbot. That's the mistake. Claude isn't a chat app. It's a multi-layer execution engine.」** — Claude の Chat/Code/MCP/Skills/CLAUDE.md の5層を組み合わせて初めて「プロンプト」から「AI チームメイトの操作」へ変わる。

出典: [[52_Claudeは多層実行エンジン Chatでない（出典）]]（HeyAnjula @HeyAnjula, 2026-07-31。本文はユーザー提供クリップから再構成）

---

## 一行で

Claude をチャットボットとして使うのが間違い。**5層（Chat/Code/MCP/Skills/CLAUDE.md）の実行エンジン**として理解し、特に **Skills + MCP** を組み合わせると、Claude は「提案する」のを止め「実行する」ように変わる。ツール接続＋スキル＋永続コンテキストの3つが揃えば、もう「プロンプト」でなく「AI チームメイトを操作」している。

## 核心: Skills + MCP が本当のアンロック

5層のうち、**Skills + MCP の組み合わせ**が境界:

- これ未満: Claude は「提案する」（チャット）
- これ以降: Claude は「実行する」（実行エンジン）

3つを組合せて「AI チームメイト」化:
1. **ツールを接続**（GitHub・Notion・Slack・Vercel 等）
2. **再利用可能なスキルをインストール**
3. **永続するプロジェクトコンテキストを与える**（CLAUDE.md）

## Claude の5層

| 層 | 役割 |
|---|---|
| **Chat** | ideas（アイデア） |
| **Code** | implementation（実装） |
| **MCP** | tool connections（ツール接続） |
| **Skills** | reusable automations（再利用可能な自動化） |
| **CLAUDE.md** | project memory（プロジェクト記憶） |

> No prompt hacking. No copy-paste loops. No tab juggling. **Just structured execution.**
>
> **The builders who understand layers win.**

---

## 本ボルト内の位置付け

**Claude Code のレイヤー構造の整理**。本ボルトはまさにこの「多層」を実装・運用している:

- **CLAUDE.md 層**（project memory）＝ 本ボルトの CLAUDE.md（運用ルール・3層構造・Ingest/Query/Lint）。[[04_カーパシーのObsidian活用術 30分で第二の脳]]（事務作業を AI に渡す・CLAUDE.md 設計5要素）の直接体現
- **Skills 層**（reusable automations）＝ [[01_Agent Skillsを作る完全プロンプト]]（手書きプロンプト）・[[01_agent-skill-creator ワークフローをAIスキル化]]（OSS 自動生成）・本ボルトのスキル運用そのもの
- **MCP 層**（tool connections）＝ [[02_Claude Code開発者ボリス推奨 MCPサーバー8選]]（Boris 8選）・[[03_mcpo MCP-to-OpenAPIプロキシ]]（相互運用インフラ）
- **Code 層**（implementation）＝ [[03_Claude Code 8時間を1時間にする10の方法]]・[[02_Claude Code 計画と実行を分けるワークフロー]]・[[06_Context Engineering Claude Codeの文脈設計]] 等の Claude Code 実践群
- **「チャットでなく実行エンジン」** は [[03_Learn Claude Code ハーネスエンジニアリング学習]]（ハーネスを見る・プロンプトでなく）と同主張。HeyAnjula が「レイヤーで捉える」、Learn Claude Code が「ハーネスを実装する」
- **「The builders who understand layers win」** は [[02_Prompt to Graph Engineering 5層の統一モデル]]（Prompt/Context/Harness/Loop/Graph の5層）の Claude Code 版。両者とも「層で捉える」が、@akshay_pachaar がエンジニアリング手法の5層、HeyAnjula が Claude 製品機能の5層

## 関連

- CLAUDE.md 層・第二の脳 → [[04_カーパシーのObsidian活用術 30分で第二の脳]]・[[CLAUDE]]
- Skills 層 → [[01_Agent Skillsを作る完全プロンプト]]・[[01_agent-skill-creator ワークフローをAIスキル化]]
- MCP 層 → [[02_Claude Code開発者ボリス推奨 MCPサーバー8選]]・[[03_mcpo MCP-to-OpenAPIプロキシ]]
- ハーネス・プロンプトでない → [[03_Learn Claude Code ハーネスエンジニアリング学習]]
- 5層モデル（別軸） → [[02_Prompt to Graph Engineering 5層の統一モデル]]
