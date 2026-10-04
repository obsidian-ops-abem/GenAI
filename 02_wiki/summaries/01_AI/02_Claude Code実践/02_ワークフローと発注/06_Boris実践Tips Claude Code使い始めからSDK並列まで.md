---
title: "06_Boris実践Tips Claude Code使い始めからSDK並列まで"
tags: [summary, ai, claude-code, boris, tips, claude-md, sdk, parallel, onboarding]
source: Boris Cherny 実践 tips トーク
speaker: Boris Cherny（Anthropic MTS・Claude Code 作成者）
created: 2026-10-04
---

# Boris 実践 Tips — Claude Code 使い始めから SDK 並列まで

> **始まりは常にコードベース Q&A（Anthropic 新入社オンボーディングの実際の手順）。「計画→承認→実装」と「結果を見るツールを与えて3回反復」。CLAUDE.md は4層（共有/個人/ネスト/全社）+権限の階層自動承認とブロック。SDK は Unix ユーティリティとしてパイプで使う。パワーユーザーは worktree で並列。CLI を選んだ理由は「ターミナルが最大公約数」と「モデル向上が速く UI 過剰投資を避ける」。**

出典: [[123_Boris実践Tips Claude Code使い始めからSDK並列まで（出典）]]（Boris Cherny。本文はユーザー提供トランスクリプト tc/output/20260929_075751 から主題別に再構成）

---

## 一行で

Claude Code 作成者 Boris による実践 tips。セットアップ→コードベース Q&A→計画と承認→コンテキスト階層→キーバインド→SDK→並列という**上達の段階**そのものを提示。

## 上達パス（核心）

1. **コードベース Q&A から**: 「このコードの使い方?」・git 履歴の質問・issues 参照・週次スタンドアップ要約。**Anthropic 新入社員の実際のオンボーディング**。派手な事から始めず質問から
2. **計画→承認→実装**: 3000行機能を衝動実装しない。「ブレインストームし計画し承認を得よ」
3. **反復の道具**: テスト/スクショを与えれば**3回反復でほぼ完璧**（初回そこそこの差を埋める）
4. **CLAUDE.md 階層管理** → 5. **SDK/パイプ** → 6. **worktree 並列**

## CLAUDE.md の4層＋権限
- **ルート CLAUDE.md**（自動読込・**チェックインで共有**）／**CLAUDE.local.md**（個人）／**ネスト CLAUDE.md**（作業ディレクトリでオンデマンド）／**enterprise**（全社）
- **短く保つ**（長い=コンテキスト消費）・`/memory` で全体確認・`#` で記憶先を選択
- **権限の階層**: 全社 bash 自動承認・**URL の永久ブロック（上書き不可）**・`.mcp.json` をチェックインしチームへ（Puppeteer MCP 等）
- 迷ったら**共有コンテキストから**（ネットワーク効果）

## SDK とパイプ
`claude -p` + 許可ツール + JSON 出力。**`git status | claude -p | jq`・巨大ログ流入・Sentry 処理**。「超知能的 Unix ユーティリティ」

## Q&A の核心
- **最難関= bash の安全性**: 危険だが毎回承認は生産性を殺す。静的解析でスケール
- **CLI の理由**: ①**ターミナルが最大公約数**（IDE 多様）②**モデル向上が速く、年末には IDE 不要の可能性→UI 過剰投資を回避**
- esc=軌道修正・esc×2=履歴・shift+tab=自動承認編集・Ctrl+R=全文

---

## 本ボルト内の位置付け

- **Boris の3講演・トークが揃う**: [[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]（思想・product overhang）＋[[10_Claude Code共同製作者 Light Cone対談]]（対談）＋本ノート（**実践の手引き**）。思想→対談→実践の順で読める
- **「コードベース Q&A から始める・Anthropic オンボーディングの実際」** は本ボルトの Query 操作（index から探し根拠付き回答）と同型の**第一歩としての質問**
- **「計画→承認→実装」** は [[02_Claude Code 計画と実行を分けるワークフロー]] の作成者本人版・[[04_Stop Vibe Coding Spec駆動開発の5ブロック]]（spec→承認）と同一原則
- **「結果を見るツールを与えて3回反復」** は [[04_Agent Harness vs Loop vs Graph Engineering]]（Loop on evidence）・[[07_Harness Engineering 壊れないAIエージェントの作り方]]（センサー）の実践一言版
- **CLAUDE.md 4層+権限階層+`.mcp.json` 共有** は [[14_Claude Code神設定入門 AGENTS.mdと検査の仕組み]]（AGENTS.md=正本+入口・約100行）・[[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]（production の versioning/permissioning）を Boris 流に整理したもの。**本ボルトの CLAUDE.md 運用の直接の拡張先**
- **「モデル向上が速く UI に過剰投資しない」** は [[11_Thoric講演 Fableフィールドガイド unhobblingとunknowns]]（例は制約になる）・Boris 講演（80%削除）と同じミニマリズムの設計判断
- **worktree 並列** は [[02_1チャットをエージェントチームへ Opus5 12ステップ]]・[[03_Graph of Loops Claude Code完全システム10リポジトリ]] で扱った隔離の原作者談
- **SDK=Unix ユーティリティ** は [[08_AIフレンドリーなCLIを開発するテクニック]] の系譜上の具体

---

## 関連

- Boris 思想講演 → [[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]
- Boris 対談 → [[10_Claude Code共同製作者 Light Cone対談]]
- 計画と実行の分離 → [[02_Claude Code 計画と実行を分けるワークフロー]]
- 証拠で反復 → [[04_Agent Harness vs Loop vs Graph Engineering]]
- CLAUDE.md/AGENTS.md 運用 → [[14_Claude Code神設定入門 AGENTS.mdと検査の仕組み]]
- production 原則 → [[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]
- worktree 隔離 → [[02_1チャットをエージェントチームへ Opus5 12ステップ]]
- CLI 設計 → [[08_AIフレンドリーなCLIを開発するテクニック]]
