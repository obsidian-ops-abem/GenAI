---
title: "10_agent-skill-creator ワークフローをAIスキル化"
tags: [summary, tool, agent-skill, workflow, oss, skill, security, evals]
source: https://x.com/trendtech33566/status/2083319809023041621
repo: https://github.com/FrancyJGLisboa/agent-skill-creator
author: trendtech33566 (@trendtech33566) / FrancyJGLisboa
published: 2026-08-01
created: 2026-08-01
---

# agent-skill-creator —— ワークフローを AI スキル化する OSS

> **「『その作業、毎回同じことしてる』を AI スキル化できる」** — 自然文の作業フローを SKILL.md に変換し、1ファイルで17プラットフォーム対応・検証/セキュリティスキャン/evals 付きで生成する OSS。

出典: [[47_agent-skill-creator ワークフローをAIスキル化（出典）]]（trendtech33566 @trendtech33566 / FrancyJGLisboa, 2026-08-01。本文はユーザー提供クリップのポストから再構成。※ポストは紹介のみで README 転記なし、詳細は言及から推定）

---

## 一行で

**自然文で書いた作業フローをそのまま AI エージェント用スキル（SKILL.md）に変換**する OSS（FrancyJGLisboa/agent-skill-creator、スター約2,000）。1つの SKILL.md で **17プラットフォーム対応**・**検証・セキュリティスキャン込み**・**evals 付き**（作って終わりでない）。定型作業の再利用化に特化。

## 解く問題

「その作業、毎回同じことしてる」—— 定型作業を AI に覚えさせたいが:

- スキル（SKILL.md）を**手書きするのは難しい**・何を書けばいいか分からない
- プラットフォームごとに**書き直しが面倒**（Claude Code・Codex・Cursor…）
- 作って**満足して終わり**（品質検証もセキュリティチェックもなし）

## 4つの特徴

| 特徴 | 内容 |
|---|---|
| **自然文→スキル化** | 自然文で書いた作業フローをそのまま SKILL.md に変換。プログラムでなく自然言語の手順書で OK |
| **17プラットフォーム対応** | 1つの SKILL.md で17の AI プラットフォーム/アシスタントに対応。書き直し不要で移植可能 |
| **検証・セキュリティスキャン込み** | スキル化と同時に検証とセキュリティスキャンを実行。安全なスキルを生成 |
| **evals 付き** | 評価セット付きで「作って終わり」にしない。継続的な品質改善が可能 |

> [!note] ポストが紹介のみのため詳細は推定
> ポストは OSS の紹介のみで README 本文の転記がない。上記4特徴はポストの箇条書き・description から再構成。正確な使い方・対応プラットフォーム一覧・evals の仕組み等は [GitHub リポジトリ](https://github.com/FrancyJGLisboa/agent-skill-creator) で要確認。

## ライセンス・状況

- GitHub: FrancyJGLisboa/agent-skill-creator
- スター（2026-08-01 ポスト時点）: 約2,000
- ※ライセンス表記はポストに未記載（リポジトリ要確認）

---

## 本ボルト内の位置付け

**Skill 作成のツール系**。本ボルトの Skill 運用ノート群を補完する実装ツール:

- [[01_Agent Skillsを作る完全プロンプト]]（@ai_ai_ailover 版・**対話型プロンプトで AI 自身に SKILL.md を書かせる**手法）の**OSS ツール化**が本作。01_ は「プロンプトの書き方」、本作は「ツールで自動化」
- 「1 SKILL.md で17プラットフォーム対応」は、[[04_カーパシーのObsidian活用術 30分で第二の脳]]（Karpathy 設計図「フォルダを読み書きできる AI ならどれでも成立」・Claude と Codex が最初に挙がる）の**スキルの移植性**を具体化。プラットフォームロックインを避ける思想
- **evals 付き（作って終わりでない）** は [[01_エージェントファクトリの作り方 ビルダーズガイド]]（no evals, no production）・[[04_Graph Architectへの20ステップ5フェーズ]]（Step 20 evaluation suite）と同じ「評価セットで継続改善」の作法。Skill も evals が必要という立場
- **セキュリティスキャン込み** は Skill のセキュリティ（インジェクション混入・権限スコープクリープ）に対する対策。[[01_MIXI新卒技術研修セキュリティ研修まとめ]]（最小権限・クレデンシャル管理）の実装側

## 関連

- Skill 作成のプロンプト手法（対になる軸） → [[01_Agent Skillsを作る完全プロンプト]]
- プラットフォーム非依存の思想 → [[04_カーパシーのObsidian活用術 30分で第二の脳]]
- evals・継続改善 → [[01_エージェントファクトリの作り方 ビルダーズガイド]]・[[04_Graph Architectへの20ステップ5フェーズ]]
- セキュリティ・最小権限 → [[01_MIXI新卒技術研修セキュリティ研修まとめ]]
