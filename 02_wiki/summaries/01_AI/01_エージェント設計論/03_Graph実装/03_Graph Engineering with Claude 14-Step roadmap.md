---
title: "03_Graph Engineering with Claude 14-Step roadmap"
tags: [summary, ai, graph-engineering, claude-code, dynamic-workflows, parallel, verifier]
source: https://x.com/0xCodez/status/2079165300625330317
author: 0xCodez (@0xCodez)
published: 2026-07-20
created: 2026-08-01
---

# Graph Engineering with Claude —— 0から graph architect への14ステップ

> **「A prompter asks a question. An architect draws a graph.（プロンプターは問い、アーキテクトはグラフを描く）」** — 直線エージェントを **fan out → reduce → synthesize** の diamond へ再構築する、Claude Code dynamic workflows に完全特化した Graph 版14ステップ完全コース。

出典: [[43_Graph Engineering with Claude 14-Step roadmap（出典）]]（0xCodez @0xCodez, 2026-07-20。本文はユーザー提供クリップから再構成）

> [!note] 同著者の Loop 版14ステップと対関係
> 著者 @0xCodez は [[01_ループエンジニアリング14ステップ]]（Loop 版14ステップ）の著者でもある。本記事はその **Graph 版**。Loop 版が「プロンプターからループ設計者へ」の道筋なら、本記事は「ループをグラフへ広げる」道筋。両者は補完関係。

---

## 一行で

直線エージェント（do A→B→C→D）は **退化したグラフ** に過ぎない。「次のステップは前の出力を読むか?」で無駄な待ちを切り落とし、独立ノードを `parallel()` で fan-out、コードで reduce、最終エージェントで synthesize する **diamond** へ再構築する。Claude Code の **dynamic workflows**（JavaScript オーケストレーション・協調はトークン消費ゼロ）で実装する、14ステップの完全コース。

## 核心: ワークの「形」がグラフ

- **プロンプト** は文、**ループ** は周期、**ハーネス** はエージェントが立つ床
- だが **ワークそのものの形**（何が何の前に走るか・何が同時に走れるか・何が全部を待つか）は **グラフ**
- ノードが思考し、エッジが結果を運ぶ
- Claude Code はこのグラフを直接構築するツールを提供: **dynamic workflows**。Claude が JavaScript オーケストレーションを書きサブエージェント群を spawn —— **協調はコードで会話でないのでモデルトークン消費ゼロ**

## 14ステップ

### 第1部: グラフの読み方を変える（01-02）

| # | ステップ | 要点 |
|---|---|---|
| **01** | **Nodes are jobs. Edges are what flows.** | ノード=作業単位、エッジ=依存（データが流れる）。**「and then」をエッジ扱いするな** —— 天気は要約を消費しない。全「and then」に問え: *次ステップは前の出力を読むか?* |
| **02** | **Your linear script is a degenerate graph** | A→B→C→D はグラフ（分岐なしチェーン）。正しいが**遅く脆い**: C が止まれば D は走らず A の作業は行き場がない。各矢印に01の問いを立て、データを運ばない矢印を切ると広い形へ崩れる |

### 第2部: 契約と fan-out/fan-in（03-07）

| # | ステップ | 要点 |
|---|---|---|
| **03** | **Give every node a contract** | 境界ある入力・境界ある出力・正確に1ジョブ。**JSON schema** で強制 —— tool-call レイヤーで validate、不一致時 Claude はリトライ（フリーテキストで解析して祈るのでない） |
| **04** | **Treat the edge as a data contract** | エッジは順序でなく**何が渡るかの約束**。実践ではエッジは**プレーン JavaScript**。reduce（flatten/dedupe/filter）はただのコード。**「結果を combine する」エージェントを spawn するな —— それは `flatMap` と `Set`、即時・ゼロトークン** |
| **05** | **Fan out with parallel()** | N 個の独立ノードは繋がず一気に展開。thunk 配列を取り1サブエージェント/thunk を spawn。(1) **barrier** —— 全 thunk を待つ。(2) throw した thunk は **null** に解決（バッチ全体を沈めない）。常に `.filter(Boolean)`。**協調層はコードなのでトークン消費ゼロ** —— 数十〜数百サブエージェントへスケール |
| **06** | **Fan in at a barrier** | 収束ノード。**真に全先行結果を一緒に必要とするときだけ barrier**。リストの flatten はエッジ（インライン）。臭い検査: parallel→transform→parallel の中間 transform にアイテム横断依存がないなら **pipeline を使って barrier をスキップすべきだった** |
| **07** | **The diamond: split → work → merge** | 全シリアスなグラフの主力トポロジー。正準形: **fan out → reduce → synthesize**。「どうステップを増やすか」でなく **「どこで分割し、どこでマージするか」** を問う —— 実際にスケールする問い |

### 第3部: 信頼度と耐障害性（08-11）

| # | ステップ | 要点 |
|---|---|---|
| **08** | **Route the edge at runtime with a conditional** | router ノードが出力を検査し下流パスを決定。ワークフローでは **JavaScript の if/switch**。決定は Claude 駆動でもルーティングはコード —— **ノードで Claude の判断、エッジでスクリプトの信頼性**。「Claude が監査をスキップした」の創発的サプライズなし |
| **09** | **Put a verifier on the edge** | verifier ノードは結果を下流へ許可する前に **発見を殺そうと試みる**。3パターン: **Adversarial verify**（N 人の懐疑家が反証、多数生存のみ保持）/ **Perspective-diverse verify**（correctness・security・reproduce の異レンズ）/ **Judge panel**（N 試行→parallel judges→勝者から synthesize）。Bun ランタイムポートで実証 |
| **10** | **Isolate nodes so one failure can't poison** | 失敗は **ノードに封じられる**。throw は null に解決・`.filter(Boolean)` が封じ込め。並列ファイル書き込み衝突は **worktree** 隔離で解決（各エージェントが git worktree で作業）。**デフォルト税でなく、必要な1トポロジーへのシートベルト** |
| **11** | **Add a cycle - but make it converge** | 未知サイズ（バグスイープ等）は **cycle**。**loop-until-dry**: K 連続ラウンドが新発見ゼロなら停止。**ほぼ全員が初回で犯す間違い**: confirmed 結果でなく **見たもの全て（everything seen）に対して dedupe する**。さもなくば拒否された発見が毎ラウンド再出現し永遠に dry にならない |

### 第4部: コスト・レイテンシ・自己ルーティング（12-14）

| # | ステップ | 要点 |
|---|---|---|
| **12** | **Tier the models across the nodes** | 全ノードが最高モデルを必要としない。退屈なノード（抽出・分類）は安いモデル、判断ノード（synthesize・裁定）に高価トークン。**大規模実行前に `/model` をチェック**、fan-out の反復ノードを下げ merge を上げる。形に触れず高価→経済的 |
| **13** | **Topology is your cost and latency** | **parallel() 対 pipeline()**。parallel barrier は全てを最遅ノードに待たせる。pipeline は各アイテムを独立ストリーム（A がステージ3の間に B はステージ1）。**デフォルトは pipeline()**。barrier は真に全結果を一度に必要とするときだけ。「separate は synchronized と同じでない」 |
| **14** | **Let Claude draw the graph - self-routing** | 事前に計画できないジョブは**手描きを止める**。dynamic workflows で目的を記述し Claude がスクリプトを自分で書く。3入り口: プロンプトで「**workflow**」と言う / 保存済みを実行（`/deep-research` = scope→parallel search→fetch→adversarial verify→synthesize）/ **ultracode** で毎実質タスクに計画。**s キー** で `.claude/workflows/` へ保存・名前で再実行 |

## 実践: 今週構築すべき6グラフ

1. **Security sweep**（route ファイルごとに1エージェント、missing auth 探索→verifier）
2. **Cited report**（`/deep-research`、3投票懐疑家で全クレームを adversarially verify）
3. **Port a module, file by file**（Bun の天井、各ファイルにテストスイートをゲート・失敗ループバック）
4. **Adversarial review of a diff**（diff サイズでルート、distinct レンズの full parallel audit→judge panel）
5. **Ecosystem scan on a schedule**（一度保存で永遠に再実行、`.claude/workflows/` で版管理）
6. **Discovery of unknown size**（finder を parallel、見たもの全てに dedupe、2ラウンド空で停止）

## 結論

> A prompter asks a question. An architect draws a graph.
>
> 直線エージェントは天井でなく最初の形。**ノードとエッジを見えるようになると**、エージェントにもっとやらせるのを止め、グラフに広くやらせ始める: 独立作業で fan-out、信頼度が重要な所でエッジを gate、判断が及ばない所でモデルを tier。**グラフを描くことを学んだ者は艦隊を走らせる。**

---

## 本ボルト内の位置付け

Graph Engineering 系の**実装完全マニュアル**。本ボルトの Graph 系ノート群の頂点に位置する:

- **概念系**: [[01_Graph Engineering エージェントを行列から解放する]]（ノード/エッジ/6トポロジー）・[[02_Graph Engineering Clearly Explained]]（nodes/edges/shared state）は「グラフとは何か」。本記事は **Claude Code dynamic workflows でどう実装するか**
- **判定系**: [[02_Graph Engineering 最大の間違い Loop↔Graph判断]]（ループはデフォルト・5シグナルで Graph 昇格）は「いつ Graph にするか」。本記事は「Graph にした後の14の実装手」
- **同著者対**: [[01_ループエンジニアリング14ステップ]]（Loop 版）と本記事（Graph 版）は、@0xCodez による **14ステップ形式の双子コース**。Loop で信頼性を担保した上で、本記事で並列へ広げる
- **検証系**: step09（verifier）は [[02_Loop Engineering Claude,GPT 実戦で効くもの]]（接地検証器）・[[01_LOOP vs GRAPH vs HARNESS ENGINEERING]]（根拠ベース停止）・[[03_AI協業の発注の型 HITL実務]]（検証の3点関与）と同じ「信頼度を構造で生む」系譜。adversarial/perspective-diverse/judge-panel の3パターンは工場スケール（[[01_エージェントファクトリの作り方 ビルダーズガイド]]）にも通底
- **隔離系**: step10（worktree）は [[02_1チャットをエージェントチームへ Opus5 12ステップ]]・[[03_Graph of Loops Claude Code完全システム10リポジトリ]]（agent-worktree G2）・[[04_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤]]（共有ベース+隔離ワークスペース）と同概念
- **収束系**: step11（loop-until-dry・見たもの全てに dedupe）は長時間運用（[[02_24時間自走する自律型AIエージェントの設計図]]）の「収束する cycle」の具体実装

## 関連

- 同著者 Loop 版14ステップ → [[01_ループエンジニアリング14ステップ]]
- グラフの概念（ノード/エッジ/トポロジー） → [[01_Graph Engineering エージェントを行列から解放する]]・[[02_Graph Engineering Clearly Explained]]
- いつ Graph にするか → [[02_Graph Engineering 最大の間違い Loop↔Graph判断]]・[[01_LOOP vs GRAPH vs HARNESS ENGINEERING]]
- verifier・接地検証 → [[02_Loop Engineering Claude,GPT 実戦で効くもの]]・[[01_エージェントファクトリの作り方 ビルダーズガイド]]・[[03_AI協業の発注の型 HITL実務]]
- worktree 隔離 → [[02_1チャットをエージェントチームへ Opus5 12ステップ]]・[[03_Graph of Loops Claude Code完全システム10リポジトリ]]・[[04_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤]]
- 学習ロードマップ版（本記事＝実装完全マニュアルの対） → [[04_Graph Architectへの20ステップ5フェーズ]]（@eng_khairallah1。Loop 必習→5パターン→信頼性→「グラフにしない」判断。本記事は Claude Code 実装、13_ は学習の道筋）
- Anthropic メソッドの実証・プロセス設計（verifier 3パターンの具体実装・Bun 実証例共有） → [[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]
