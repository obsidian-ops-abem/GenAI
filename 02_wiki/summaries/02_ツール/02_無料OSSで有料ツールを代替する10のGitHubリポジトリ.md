---
title: "02_無料OSSで有料ツールを代替する10のGitHubリポジトリ"
tags: [記事まとめ, oss, tools]
source: https://x.com/unicodef1wn/status/2082200846243143795
author: unicode (@unicodef1wn)
published: 2026-07-29
created: 2026-07-29
status: 未着手
---

# 無料OSSで有料ツールを代替する10のGitHubリポジトリ

> [!info] 出典
> unicode（@unicodef1wn）、2026年7月29日5:25公開（Xポスト）
> [元投稿](https://x.com/unicodef1wn/status/2082200846243143795)
> 詳細メタデータ: [[01_33 Free GitHub Repositories（出典）]]

年間$25,000規模の有料SaaSを無料OSSで代替できる、という趣旨のリポジトリ紹介ポスト。見出しは「11件・合計150万スター以上」とあるが、本文で列挙されているのは10件。同著者の長文記事「33 Free GitHub Repositories That Can Replace $25,000/year in Paid Software」からの抜粋と見られる。

## リポジトリ一覧

| # | リポジトリ | スター | 内容 |
|---|---|---|---|
| 1 | [free-for-dev](https://github.com/ripienaar/free-for-dev) | 131k | 永久無料ティアを提供する数百のサービス一覧 |
| 2 | [public-apis](https://github.com/public-apis/public-apis) | 453k | 1,500以上の無料API（天気・金融・画像・ゲーム等） |
| 3 | [awesome-selfhosted](https://github.com/awesome-selfhosted/awesome-selfhosted) | 309k | Notion・Google Photos・Zapier等、数十のサブスクのセルフホスト代替 |
| 4 | [awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code) | 51k | Claude Code向けのスキル・フック・スラッシュコマンド・オーケストレーター集 |
| 5 | [anthropics/skills](https://github.com/anthropics/skills) | 165k | Anthropic公式のスキルリポジトリ |
| 6 | [awesome-mcp-servers](https://github.com/punkpeye/awesome-mcp-servers) | 91k | 数千のMCPサーバー一覧（ブラウザ・DB等への接続） |
| 7 | [awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) | 116k | 100以上のAIエージェント／RAGアプリの実装例（コード付き） |
| 8 | [system-prompts-and-models-of-ai-tools](https://github.com/x1xhlol/system-prompts-and-models-of-ai-tools) | 142k | Cursor・Devin・v0・Claude Codeのリーク系システムプロンプト集 |
| 9 | [awesome-codex-skills](https://github.com/ComposioHQ/awesome-codex-skills) | 15k | Codex向けスキル集（データ分析・ライティング・生産性・開発） |
| 10 | [awesome](https://github.com/sindresorhus/awesome) | 490k | 「あらゆる他のリストのリスト」 |

## 所感・関連

- 10件中6件（4・5・6・7・8・9）が、このボルトのAI/agent-engineering系ノートと直結する。特に `anthropics/skills` と `awesome-mcp-servers` は、このボルトの `CLAUDE.md` 運用や以前調べた `redmine-mcp-server` の話と実務的に繋がりそう
- 8番目の `system-prompts-and-models-of-ai-tools` は「リークされたシステムプロンプト集」。真正性は保証されておらず、内容を信用する前に個別の検証が要る
- 著者は「無料・節約」訴求を専門にするアカウントで、リプライ欄も好意的な反応が並ぶ典型的なバイラル投稿の構成。ただし個々のリポジトリ名・スター数・内容自体は検証可能な事実であり、実用性は高い
- 見出しの「11件」と本文の「10件」が一致していない。元記事は「33件」とさらに多いリストで、今回はその抜粋と見られる

## 次にやること

- [ ] `anthropics/skills` と `awesome-mcp-servers` を実際に覗き、このボルトのCLAUDE.md運用や[[03_intent-system 意図駆動開発のオーケストレーションCLI]]の話に転用できるものがないか確認する
- [ ] `system-prompts-and-models-of-ai-tools` の内容の真正性を個別に確認する

関連: [[index]]
