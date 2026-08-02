---
title: "05_openai codex-plugin-cc Claude CodeからCodexへ委譲"
tags: [summary, ai, claude-code, codex, mcp, plugin, multi-model, review]
source: https://github.com/openai/codex-plugin-cc
author: OpenAI（公式）
created: 2026-08-02
---

# openai/codex-plugin-cc — Claude Code から Codex へ委譲

> **Claude Code 内から Codex を呼び出す OpenAI 公式プラグイン。`/codex:review`（読取専用レビュー）・`/codex:adversarial-review`（挑戦的レビュー）・`/codex:rescue`（タスク委譲）で、Claude（orchestrator/判断）と Codex（worker/検証・実装）の分業を公式に支える。**

出典: [[64_openai codex-plugin-cc Claude CodeからCodexへ委譲（出典）]]（OpenAI 公式・GitHub。本文はクリップ README を使用）

---

## 一行で

OpenAI が公開した Claude Code プラグイン。Claude Code のワークフロー内から Codex を呼び出し、コードレビュー・タスク委譲・バックグラウンドジョブ管理を行う。[[04_Claudeはorchestrator専念 hook強制の分業]]（@cursorvers）の「Claude orchestrator + 別モデル worker」を**公式に支えるプラグイン**。

## コマンド群（核心）

| コマンド | 役割 | 特徴 |
|---|---|---|
| `/codex:review` | 通常の読取専用 Codex レビュー | 未コミット変更 / ブランチ比較(`--base`)。`--background`/`--wait`対応。**steerable でない**（focus text 取れない） |
| `/codex:adversarial-review` | **挑戦的**レビュー | 選択実装・設計を疑う。前提・トレードオフ・失敗モード・代替手法を圧力テスト。auth/データ損失/ロールバック/競合/信頼性の特定リスク領域。focus text 取れる。**読取専用・コードを修正しない** |
| `/codex:rescue` | タスク委譲 | `codex:codex-rescue` サブエージェント経由。バグ調査・修正試行・前回 Codex タスクの続き |
| `/codex:transfer` | セッション引き継ぎ | |
| `/codex:status` `/codex:result` `/codex:cancel` | バックグラウンドジョブ管理 | 進捗確認・結果取得・キャンセル |

## 要件・インストール

- ChatGPT サブスク（Free 含む）または OpenAI API キー（Codex 利用制限に消費）
- Node.js 18.18+
- インストール: `/plugin marketplace add openai/codex-plugin-cc` → `/plugin install codex@openai-codex` → `/reload-plugins` → `/codex:setup`

## adversarial-review の価値

`/codex:review` は「コード詳細のレビュー」だが、`/codex:adversarial-review` は**「方向性を疑う」**:
- 出荷前に「コード詳細でなく方向を挑戦する」レビュー
- 設計選択・トレードオフ・隠れた前提・代替アプローチに焦点
- auth / データ損失 / ロールバック / 競合状態 / 信頼性 等の特定リスク領域を圧力テスト
- コードを修正せず（読取専用）・異なるアプローチがより安全・シンプルでなかったかを問う

---

## 本ボルト内の位置付け — orchestrator/worker 分業の公式実装

本プラグインは **[[04_Claudeはorchestrator専念 hook強制の分業]]（@cursorvers）の「Claude orchestrator + 別モデル worker」を OpenAI 公式に支える道具**:
- @cursorvers は hook で Claude の直接編集を block し worker へ委譲した。本プラグインはその「Codex への委譲」を公式コマンドで提供
- **adversarial-review（別モデルによる挑戦的レビュー）** は「判断と実装を同じモデルに握らせない」自己採点排除（[[02_1チャットをエージェントチームへ Opus5 12ステップ]]・[[02_Loop Engineering Claude,GPT 実戦で効くもの]]・[[04_自己レビューエージェントのGraph設計 Anthropicメソッド]] と同系）の実装
- **ccc との接続**: ccc の査（独立した検証ロール）を「Codex という別モデル」で実現する簡易版。[[03_ccc関連事例調査 ボルト内の同じアプローチ]] 参照
- **読取専用**（review/adversarial-review はコードを修正しない）は、検証器が実装を握らない安全設計

## 既存ノートとの接続

- **Claude orchestrator + 別モデル worker** → [[04_Claudeはorchestrator専念 hook強制の分業]]（本プラグインが公式に支える道具）
- **adversarial review（挑戦的検証）** → [[03_Graph Engineering with Claude 14-Step roadmap]]（Step9 verifier 3パターンのうち adversarial）・[[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]（見えない2人のレビューア）
- **読取専用・コード修正しない検証器** → [[03_Graph of Loops Claude Code完全システム10リポジトリ]]（G4 Phase6 コードゲート・モデルでなく `validate_ledger.py`）
- **MCP/プラグイン系** → [[02_Claude Code開発者ボリス推奨 MCPサーバー8選]]（Boris の3原則）・[[04_Claudeは多層実行エンジン Chatでない]]（MCP層）

---

## 関連

- Claude orchestrator + worker 分業（公式が支える道具） → [[04_Claudeはorchestrator専念 hook強制の分業]]
- ccc の査（検証ロール）の別モデル実装 → [[03_ccc関連事例調査 ボルト内の同じアプローチ]]
- adversarial verifier → [[03_Graph Engineering with Claude 14-Step roadmap]]
- 読取専用検証器 → [[03_Graph of Loops Claude Code完全システム10リポジトリ]]
- MCP/Skills 層 → [[04_Claudeは多層実行エンジン Chatでない]]
