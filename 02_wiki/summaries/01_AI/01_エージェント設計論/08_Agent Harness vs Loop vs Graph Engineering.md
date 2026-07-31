---
title: "08_Agent Harness vs Loop vs Graph Engineering"
tags: [summary, ai, agent-design, harness, loop, graph]
source: https://x.com/LunarResearcher/status/2082076425465762082
article_url: https://x.com/i/article/2082042903384489984
author: Lunar (@LunarResearcher)
published: 2026-07-28
created: 2026-07-31
---

# Agent Harness vs Loop vs Graph Engineering

> **3つは「競合する方法論」ではなく「同じシステムの異なる部分」。** Harness は基盤レイヤー、Loop/Graph はその上で選ぶ制御フロー。

出典: [[27_Agent Harness vs Loop vs Graph Engineering（出典）]]（Lunar @LunarResearcher, 2026-07-28。本文はX articleログイン壁のため解説記事群から再構成）

---

## 一行で

エージェント設計の3つの「○○ Engineering」を、**基盤レイヤー（Harness）**と**制御フロンの2替代（Loop / Graph）**として整理し、3層が協調する単一アーキテクチャだと説く概念解説。

## 3つの定義（核心）

| 概念 | 責務 | 一言 | 種類 |
|---|---|---|---|
| **Harness** | 環境・インフラ・復旧 | ***where* it runs / *what* it can touch / *how* it recovers** | 基盤（必須・併用） |
| **Loop** | 反復の制御フロー | ***what* the agent does / *when***。work→feedback→iterate | 制御フロー（線形） |
| **Graph** | グラフ構造の制御フロー | ノード/エッジで接続を定義。特化ステップ＋ハンドオフ | 制御フロー（非線形） |

**最も重要な区別**:
- **Loop と Graph は制御フロンの代替選択**（タスクごとにどちらか）
- **Harness はどちらとも併用する別の基盤レイヤー**（本番運用に必須）

## 具体例: バグ修正エージェント

- **Harness** — ファイルを開く/編集する能力を与える翻訳層、サンドボックス、観測性
- **Loop** — テストが壊れたら学んで再試行。「次に何を、いつ止めるか」
- **Graph** — 単一エージェントが詰まったら、再現→分析→修正→検証 に分割してハンドオフ

## 怠るとどうなるか

| 層 | 怠ると |
|---|---|
| Harness | 完璧な loop があっても「本番で壊れる・復旧不能・観測不能」 |
| Loop | 「永遠にループ・停止・早すぎる終了」 |
| Graph | （過剰なら）トークンコスト増だけ。不足なら単一ループが記憶過多/待ち状態 |

## 主要な主張

1. **3層は競合ではない** — "in reality they are different parts of the same system"。階層的アーキテクチャとして協調（Vin Vashishta）
2. **段階的移行パス** — 層構造により、企業は技術的一気作り変えではなく段階的に AI エージェントへ移行できる
3. **「agent は機能名ではなく運用問題」**（@mycomputerspot リプライ）— ファイル・APIが絡むと Harness 層が運用上の本質に
4. **Graph は「確立されたアプローチの最新の名称」**（LangChain）— 全く新しいものではなく、Loop/Harness 工学と連続する

## いつどちらを選ぶか

- **Loop から始める**（基本）→ [[05_ループエンジニアリング14ステップ]]
- **Graph へ**: 非線形・分岐・並列、複数特化ステップ間の状態共有、単一ループの記憶過多/待ち状態 → [[07_Graph Engineering Clearly Explained]]
- **Harness は常に**: 環境・ツール・復旧・観測性なしに本番運用は不可

---

## 本ボルト内の位置付け

本記事は、2026年7月にXで流行した「○○ Engineering」系譜の1つ。本ボルトには既に同系譜のノートが複数あり、本記事はそれらを**統合的に見渡す**役割:

- [[02_LOOP vs GRAPH vs HARNESS ENGINEERING]] — 同3層の診断フレーム
- [[04_Prompt to Graph Engineering 5層の統一モデル]] — Prompt/Context/**Harness**/Loop/Graph の5層（前編）
- [[07_Graph Engineering Clearly Explained]] — Graph 層の実践解説（続編）
- [[03_LOOP→GRAPH→HARNESSパイプラインを一気通貫で実装する]] — 3層の実装チュートリアル

## 再構成の確度について

本文は X article のログイン壁で直接取得不可。しかし3概念の定義は **Vin Vashishta（Substack）/ MindStudio / Medium / Towards AI / TrueFoundry / DevCompass** の複数独立情報源で完全一致しており、再構成の確度は高い。正確な原文引用が必要な場合は要ログイン。

---

## 関連

- 3層診断フレーム → [[02_LOOP vs GRAPH vs HARNESS ENGINEERING]]
- 5層統一モデル（前編） → [[04_Prompt to Graph Engineering 5層の統一モデル]]
- Graph 層の深掘り（続編） → [[07_Graph Engineering Clearly Explained]]
- 3層の実装 → [[03_LOOP→GRAPH→HARNESSパイプラインを一気通貫で実装する]]
- Loop 層のロードマップ → [[05_ループエンジニアリング14ステップ]]
