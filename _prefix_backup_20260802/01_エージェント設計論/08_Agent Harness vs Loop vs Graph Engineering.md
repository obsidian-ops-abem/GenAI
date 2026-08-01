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

出典: [[27_Agent Harness vs Loop vs Graph Engineering（出典）]]（Lunar @LunarResearcher, 2026-07-28。2026-08-02 に著者原文全文を取得済み。当初はログイン壁のため解説記事群から再構成していたが、原本と定義が完全一致することを確認）

---

## 一行で

エージェント設計の3つの「○○ Engineering」を、**基盤レイヤー（Harness）**と**制御フロンの2替代（Loop / Graph）**として整理し、3層が協調する単一アーキテクチャだと説く概念解説。

## 30秒でわかる3層（原文の序論）

**Environment → Feedback → Flow** という暗記モデル:

- **Harness**（環境）— モデルに tools / memory / control / workspace を与える
- **Loop**（フィードバック）— 仕事をどう再試行・検査・改善するかを決める
- **Graph**（フロー）— 次に何のステップが許されるかを定義する

## 3つの定義（核心）

| 概念 | 責務 | 一言 | 種類 |
|---|---|---|---|
| **Harness** | 環境・インフラ・復旧 | ***where* it runs / *what* it can touch / *how* it recovers** | 基盤（必須・併用） |
| **Loop** | 反復の制御フロー | ***what* the agent does / *when***。work→feedback→iterate | 制御フロー（線形） |
| **Graph** | グラフ構造の制御フロー | ノード/エッジで接続を定義。特化ステップ＋ハンドオフ | 制御フロー（非線形） |

**最も重要な区別**:
- **Loop と Graph は制御フロンの代替選択**（タスクごとにどちらか）
- **Harness はどちらとも併用する別の基盤レイヤー**（本番運用に必須）

## Harness が含む6要素（原文）

1. **Context Injection** — instructions / retrieved knowledge / conversation state / memory / policies / task rules
2. **Action Surfaces** — API / browser / shell / code exec / MCP / DB / custom functions
3. **Persistence** — files / checkpoints / session state / progress logs / git / long-term memory
4. **Execution Control** — retries / timeouts / budgets / model selection / subagent spawning / approval gates
5. **Safety And Governance** — least-privilege / isolation / allowlists / secret handling / human approval
6. **Observability** — traces / tool I/O / state transitions / latency / cost / eval results

> 同じモデルでも、clean tools + stable state + structured memory + clear permissions + observable execution を与えたチームと、vague prompt + messy tools + noisy context を与えたチームで結果は全く違う。「モデルが同じでも動作条件が違う」。

## Loop の8構成要素（原文）

Trigger（起動）/ Goal（具体的な到達条件）/ State（次サイクルに必要な情報）/ Action Policy（許される行動）/ Evidence（成功の証拠）/ Feedback（何が失敗したか、簡潔かつ実行可能）/ Stop Rule（success / timeout / budget exhausted / max retries / irrecoverable / escalation）

**Loop の最重要原則（原文強調）**:

> **Do not loop on confidence. Loop on evidence.**（自信でなく証拠でループせよ）

「エージェントが done と言った」は停止条件ではない。tests pass / schema validates / citations resolve / reviewer approves / policy check clean が本物の停止条件。

## Graph は何を設計するか（原文）

Node Boundaries（何が deterministic function / LLM call / specialist agent / human review に属するか）/ State Schema / Routing Conditions / Concurrency / Cycles And Exits / Durability（チェックポイントと再開）

Graph が価値を持つのは: 意味のある分岐 / 承認 / スペシャリストの引継ぎ / 並列 / リカバリパス / 明示的な制御点を持つ複数ステップワークフロー。「1人のエージェントにツールを渡して任せる」だけなら harness + loop で十分。**早すぎるグラフはシステムを脆くする**（→ [[10_Graph Engineering 最大の間違い Loop↔Graph判断]] と同系）。

## 診断ルール（原文・実用的）

失敗の種類で直す層を変える:

- **エージェントが動けない** → harness を直す（missing tool access / stale state / weak memory / bad permissions / no observability）
- **ほぼ動くが信頼性がない** → loop を直す（first draft is close but weak / success inconsistent / retries uncontrolled / no proof of completion）
- **プロセス自体が複雑** → graph を直す（many specialists / approvals / branching / parallel paths / structured handoffs）

## 5つのよくある間違い（原文）

1. **早すぎるグラフ構築** — まずシンプルな harness でトレースを集め、安定パターンを見つけてから形式化
2. **同じモデルに書かせて採点させる** — 同じ盲点を共有。決定的チェック / 別レビューア文脈 / 外部評価器 / 高影響行動への人間承認を（→ [[14_自己レビューエージェントのGraph設計 Anthropicメソッド]] と同系）
3. **「 Keep Trying」をループにする** — 無制御のコスト漏れ。測定可能な目標・証拠・再試行制限・エスカレーション規則が必要
4. **Harness を雑多な引き出しにする** — ツールが多いほど選択ミス・ノイズ・信頼性低下・リスク面拡大。良い harness は混雑でなく精密
5. **オーケストレーション失敗をモデルのせいにする** — モデルは壊れたAPI / 古いstate / 終了条件欠落 / 曖昧なtool schema / 見えない失敗モードを補償できない

## 3層が協調する例: research-and-publishing agent（原文）

- **Harness** — browser / search / file workspace / memory / citations / approvals / traces / model routing を提供
- **Loop** — 証拠が弱いときソース取得を再試行・引用失敗の修正・grader checks・市場変化時のリフレッシュ
- **Graph** — scoping → research → screening → synthesis → drafting → review → publication のパスを制御（公開前に human gate）

## 主要な主張

1. **3層は競合ではない** — 同じシステムの異なる部分。階層的アーキテクチャとして協調。「A perfect graph will not save a weak harness. A strong harness will still waste money without good loops.」
2. **モデルは差別化要因ではない** — 本番での差はモデルでなく、周囲のシステム（harness / loops / graph）で生まれる
3. **「agent は機能名ではなく運用問題」**（@mycomputerspot リプライ）— ファイル・APIが絡むと Harness 層が運用上の本質に

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

## 本文取得状況

2026-07-31 時点では X article のログイン壁で本人本文が直接取得できず、Vin Vashishta（Substack）/ MindStudio / Medium / Towards AI / TrueFoundry / DevCompass の複数独立解説から再構成していた。2026-08-02 に著者原文全文を取得（出典ページ [[27_Agent Harness vs Loop vs Graph Engineering（出典）]] 参照）。再構成版の3層定義は原本と完全一致を確認済み。原文には再構成版に含まれていなかった Environment→Feedback→Flow の暗記モデル・Harness 6要素・Loop 8構成要素・「Loop on evidence」の最重要原則・診断ルール・5つの間違い・プロダクションチェックリストが含まれており、本要約に反映済み。

---

## 関連

- 3層診断フレーム → [[02_LOOP vs GRAPH vs HARNESS ENGINEERING]]
- 5層統一モデル（前編） → [[04_Prompt to Graph Engineering 5層の統一モデル]]
- Graph 層の深掘り（続編） → [[07_Graph Engineering Clearly Explained]]
- 3層の実装 → [[03_LOOP→GRAPH→HARNESSパイプラインを一気通貫で実装する]]
- Loop 層のロードマップ → [[05_ループエンジニアリング14ステップ]]
