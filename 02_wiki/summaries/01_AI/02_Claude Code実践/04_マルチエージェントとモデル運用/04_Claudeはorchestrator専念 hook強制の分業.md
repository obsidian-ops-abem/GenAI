---
title: "04_Claudeはorchestrator専念 hook強制の分業"
tags: [summary, ai, claude-code, multi-agent, orchestrator, hook, separation-of-duties]
source: https://x.com/cursorvers/status/2083442835500781699
author: 大田原正幸｜医療AIガバナンス｜Cursorvers（@cursorvers）
published: 2026-08-01
created: 2026-08-02
---

# Claude は orchestrator 専念・hook 強制の分業

> **Claude にコードは書かせない。Claude（高価）は判断・統合・検証・合議だけを担う orchestrator とし、実装は別モデル（Codex/GLM/Kimi/Grok/NIM/ローカルLLM）の worker へ。分業の境界は規約でなく hook で構造的に強制し、自己採点の構図を物理的に排除する。**

出典: [[62_Claudeはorchestrator専念 hook強制の分業（出典）]]（@cursorvers, 2026-08-01。本文はクリップ全文＋コメントを使用）

---

## 一行で

@cursorvers による個人開発の Claude 運用。Claude を orchestrator 専念させ、実装を多様な worker モデルに委譲し、その境界を hook（Claude が直接編集しようとすると block・subagent 丸投げは default-deny）で強制する。「判断と実装を同じモデルに握らせると自己採点になる」という分業の構造化。

## 役割分担（核心）

| 層 | 役割 | モデル |
|---|---|---|
| **orchestrator** | 要件の対話・タスクの振り分け・成果物の検証・合議の判定 | **Claude（高価・判断と統合にだけ使用）** |
| **worker** | 実装・レビューの実務 | Codex / GLM / Kimi / Grok / NVIDIA NIM (Nemotron 3 Super 120B) / さくらのAI Engine / ローカルLLM (Qwen3.6-35B-A3B) 等。適時入れ替え多様性確保 |

## hook による構造的強制（肝）

- Claude がコードを直接編集しようとすると **hook が block** → 委譲へ回るしかない
- Claude 内部の subagent（Sonnet/Haiku）への丸投げも **default-deny** で封じ、抜け道なし
- **分業の境界を規約でなく構造で守る**のがこの使い方の肝

## なぜこうするか（自己採点の排除）

> 判断と実装を同じモデルに握らせると、**自分の成果物を自分で採点する構図になり、検証が甘くなる**から

- 高価な Claude は判断と統合にだけ使い、手数の要る仕事は各モデルの得意分野へ流す
- worker に多様なモデルを適時入れ替えるのは**多様性の確保**（単一モデルの盲点を避ける）

## 要件対話のコツ（リプライより）

固定5項目の型はあるが毎回フル回転させず、実際に効いているのは2つ:
1. **質問を自由記述にせず AI 側が選択肢を作って選ばせる**
2. **着手前に完了条件だけは必ず決める**

残りは案件の重さ次第で省く。

---

## 本ボルト内の位置付け — ccc と同じ思想

本ポストは **ccc（Claude-Code-Communication）の5ロール設計と同じ思想の個人開発版**:
- **采/計（orchestrator）と作（worker）の分離** = Claude orchestrator 専念・worker 別モデル
- **査の独立・実装禁止** = 「判断と実装を同じモデルに握らせない」自己採点排除
- **Tier/権限の構造化** = hook による block・default-deny（ccc の main保護・Forgejo Actions ゲートと同方向）
- 詳細は [[03_ccc関連事例調査 ボルト内の同じアプローチ]] の tier1 #1・#2 と照合

ccc は Redmine チケットでプロセス間協調・5ロール横断だが、本ポストは **1人開発者が hook 1つで同じ「判断/実装分離」を実現する最小実装**。ccc の思想がプロセス重型なら、本ポストはフック軽量型。

## 既存ノートとの接続

- **「作った者は自分を採点しない」** → [[02_1チャットをエージェントチームへ Opus5 12ステップ]]（verifier は仕事しなかった別役割）・[[02_Loop Engineering Claude,GPT 実戦で効くもの]]（接地した検証器・自己採点禁止）・[[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]（judge 先構築）と完全に同系
- **「hook で構造的強制・規約でなく」** → [[03_CLINEに全部賭けろ コーディングエージェント時代のプログラマ]]（設定は安全網で設計ではない・deno パーミッション）・[[01_エージェントファクトリの作り方 ビルダーズガイド]]（権限はモデル外で強制・プロンプトは決定に入らない）と同根
- **「Claude 内部 subagent 丸投げ default-deny」** → [[02_1チャットをエージェントチームへ Opus5 12ステップ]]（ワーカーの tools から Agent 削除＝構造的キャップ）の実践
- **「worker に別モデル」** → [[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]（dynamic workflows で数千エージェント・モデル毎ルーティング）・[[05_Claude Codeの6層アーキテクチャ ダムループ]]（エージェントチームは完全な Claude Code インスタンス）の変形（同モデルでなく異モデル混交）
- **「着手前に完了条件」** → [[04_Stop Vibe Coding Spec駆動開発の5ブロック]]（Tasks の完了条件）・[[01_Agent Harness vs Loop vs Graph Engineering]]（Loop の stop rule・証拠で止まる）と同系
- **「質問は選択肢で」** → [[03_AI協業の発注の型 HITL実務]]（決め手ポイントを1問ずつ）に相似

---

## 関連

- ccc と同じ思想（プロセス重型） → [[03_ccc関連事例調査 ボルト内の同じアプローチ]]
- verifier 独立・自己採点禁止 → [[02_1チャットをエージェントチームへ Opus5 12ステップ]]
- 接地した検証器 → [[02_Loop Engineering Claude,GPT 実戦で効くもの]]
- 権限の構造的強制（設定でなく設計） → [[03_CLINEに全部賭けろ コーディングエージェント時代のプログラマ]]
- 完了条件を着手前に → [[04_Stop Vibe Coding Spec駆動開発の5ブロック]]
- HITL承認・決め手ポイント → [[03_AI協業の発注の型 HITL実務]]
