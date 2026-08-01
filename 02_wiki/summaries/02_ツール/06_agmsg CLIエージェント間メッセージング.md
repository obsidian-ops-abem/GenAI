---
title: "06_agmsg CLIエージェント間メッセージング"
tags: [summary, tool, cli-agent, messaging, oss]
source: https://x.com/fujibee/status/2083113453674459295
repo: https://github.com/fujibee/agmsg
author: fujibee (@fujibee)
published: 2026-07-31
created: 2026-08-01
---

# agmsg —— CLI AI エージェント間メッセージング

> **「コミュニティが agmsg に強い輪郭を与えてくれている」** — Claude Code / Codex / opencode 等、CLI で動く AI エージェント間でメッセージをやり取りする OSS。v1.1.12 は機能追加でなく**コミュニティ主導の成熟**を示す。

出典: [[35_agmsg CLIエージェント間メッセージング（出典）]]（fujibee @fujibee, 2026-07-31）

---

## 一行で

CLI で動く AI エージェント（Claude Code / Codex / opencode 等）間でメッセージをやり取りするための OSS。エージェント間連携のインフラで、コミュニティによる周辺ツール（可視化・監視）が独立に育つエコシステムを形成。

## v1.1.12 の目玉 = コミュニティ（機能でない）

### 1. opencode モニターモード対応（完全コミュニティ発）
@JulikaAiblinger さんが opencode の監視不在課題を解決:
- **opencode-sentinel** プラグイン自作 → agmsg 側対応も fork で実装 → ADR（設計記録）まで作成
- sentinel がない環境では**従来動きに自動切替**の配慮が最初から

### 2. codex ブリッジ修正（コミュニティの実測が真因特定）
- @8CEVSmSRMT32119 が「動かない・1日潰した」→ note に**コードレベル真因記事**:
  - Windows で起動だけで **29秒**
  - タイムアウト起点が起動後に計算されるため**必ず強制終了**する機序を実測
- これで @fujibee 側の修正は **2行**。報告→修正まで1日。

### 3. メッセージ可視化ツールがコミュニティ製だけで5つに
- **agmsg-bubblelog**（@dreiachse）—— Claude Code×Codex を **LINE 風**に表示
- **agmsg-tui**（rrrrnmtsu）—— Rust ratatui 製、SSH/tmux 内から使用可能
- → 同じメッセージ土台上で**見せ方が独立に育つ**

### 今回の外部貢献（8人分以上）
@Masashi_Ono0611（project_path 検証）/ @chemica_tan（Windows 誤判定2件）/ @otsune（PowerShell 安全な使い方の文書化）

## インストール

```
npx agmsg@latest install --update
```

GitHub: fujibee/agmsg / ショーケース: agmsg.cc

## 設計の教訓

- **「実際に使っている人の実測がなければ直せなかった」** —— Windows 重環境問題は現場の実測が真因特定の鍵
- **コミュニティ発の機能**（opencode 対応）が本家還元される健全な OSS 運営
- メッセージ層と可視化層の分離 —— 「土台は1つ、見せ方は独立に育つ」

---

## 本ボルト内の位置付け

マルチエージェント協調の**通信インフラ**。[[02_1チャットをエージェントチームへ Opus5 12ステップ]]（サブエージェントチーム）や [[03_Graph of Loops Claude Code完全システム10リポジトリ]]（Graph+Loop）で語られるエージェント間連携を、**実際のメッセージパッシング**で支える土台。コミュニティ主導の成熟パターンは、OSS 運営の参考事例として価値。

## 関連

- マルチエージェント協調 → [[02_1チャットをエージェントチームへ Opus5 12ステップ]]・[[03_Graph of Loops Claude Code完全システム10リポジトリ]]
- エージェント運用基盤 → [[02_24時間自走する自律型AIエージェントの設計図]]・[[01_エージェントファクトリの作り方 ビルダーズガイド]]
