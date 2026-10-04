---
title: "06_ADKワークショップ 仮想試着室とADK2グラフワークフロー"
tags: [summary, ai, adk, google, graph-workflow, multi-agent, flutter, dynamic-workflow]
source: Google ワークショップ＋ADK2ライブラボ（AI Camp）
speaker: Google WS講師陣／Anyuan
created: 2026-10-04
---

# ADK ワークショップ — 仮想試着室と ADK2 グラフワークフロー

> **前半: ADK Go×Flutter でEC体験（4エージェント・session/artifacts・state=共有メモリ・大きな画像は参照のみ返す）。後半: ADK2 の3パターン — グラフ（functional+join+排他ルーティングで決定的部分を AI から剥離）、コラボレーティブ（chat=ハンドオフ/single-turn=並列ツール/task=人間入力可）、ダイナミック（実行時木生成+再帰深さ制限）。「ワークフロー全体が決定的で AI は戦略ノードだけ=トークン節約」。**

出典: [[119_ADKワークショップ 仮想試着室とADK2グラフワークフロー（出典）]]（Google WS＋Anyuan ADK2ラボ。本文はユーザー提供トランスクリプト tc/output/20261003_041136 から主題別に再構成）

---

## 一行で

Google ADK の実践ワークショップ2部構成。前半は ADK Go で仮想試着室+スタイリングのフルスタック構築、後半は ADK2 のグラフ/コラボレーティブ/ダイナミックの3パターンを任何分けする設計判断。

## 前半の要点（ADK Go アプリ構築）

- **Artifacts パターン**: 大きな画像をレスポンスで返さず GCS 保存+参照名のみ。「軽く・速く・予測可能」
- **セッション使い分け**: 試着=毎回新規／スタイリング=**同一ID再利用でフィードバック記憶**
- **state=共有メモリ**: エージェント間の直接通信なし。試着が artifact 名を書き→スタイリングが読む
- **agent_as_tool**: カタログエージェントをツール化して委譲
- **instructions.md を分離**し Go embed で読み込む（プロンプトとコードの分離）
- **同じツール・違う指示**でスタイリング/試着が別エージェントになる

## 後半の核心（ADK2 の3パターン）

### グラフ（事前にフローが分かる場合）
- **functional ノード**: fetch/validate 等の決定的ロジックを AI から剥離。「fetch を agent にさせるとトークン消費・信頼性低下・コスト増。functional なら LLM 呼び出しゼロ」
- **並列 fan-out → join → 戦略 agent**: 全体決定的・**AI は戦略ノードだけ**
- **決定的 vs 非決定的ルーター**: 明確なルール（温度>78等）は決定的。エッジケース多数・列挙不能は LLM ルーター（**重い推論モデルをルーターに使わない**）

### コラボレーティブ（インテントベース）
| モード | 挙動 | 適用 |
|---|---|---|
| **chat**（デフォルト） | 制御を**ハンドオフ**（並列不可・会話移る） | 単純委譲 |
| **single-turn** | **ツールとしてwrap**（並列可・人間入力不可） | 並列スペシャリスト |
| **task** | single-turn＋**一時停止して人間入力**（同一セッションで再開） | HITL |

### ダイナミック（木構造が事前に不明）
- ディープリサーチ型。**実行時にトピック分解→dynamic fan-out→再帰**
- **再帰深さ（max depth）と幅を制限**し無限展開を防止

### いつ何を使うか
単純なら ADK1 で十分／グラフは必須でない（**単一エージェント最適・AI 不要最適な場合もある**）／事前描画可能→グラフ／インテント+チーム+HITL→コラボレーティブ／構造不明→ダイナミック

---

## 本ボルト内の位置付け

- **「決定的部分を AI から剥離（functional/rule routing）」** は [[02_Jev Engineering 決定と生成を分離するSystem One]]（判断と生成の分離）・[[07_Harness Engineering 壊れないAIエージェントの作り方]]（決定論的チェック・権限外強制）と同じ系譜の ADK 版。**LLM 呼び出しゼロの function ノード**は Jev の「モデルを完全にスキップする純コード分岐」と同思想
- **chat/single-turn/task の3モード** は [[02_1チャットをエージェントチームへ Opus5 12ステップ]]（ハンドオフ vs ツール化）・[[08_LangGraph Academy エージェント構築のコース]] の概念を ADK 用語で整理。**task モード=同一セッションで HITL** は interrupt の実装形
- **state=共有メモリ（直接通信なし）** は [[08_LangGraph Academy エージェント構築のコース]]（state schema・reducer）・ccc（[[03_ccc関連事例調査 ボルト内の同じアプローチ]]・Redmine チケット=state）と同根。黒板的協調
- **join ノード=バリア+schema 保証** は [[05_Graph Engineering 入門 What It Is]]（diamond の収束・checker）の実装名
- **ダイナミック+再帰深さ制限** は [[05_Graph Engineering 入門 What It Is]]（dynamic は2番手）・[[03_Graph Engineering with Claude 14-Step roadmap]]（dynamic workflows 自己ルーティング）に**深さ制限という安全弁**を加える
- **Artifacts/session/state** の3プリミティブは [[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]（production 4原則の versioning/portability）の ADK 実装
- 前半の Flutter 側は本ボルト主軸外だが「UI=stateの関数・MVVM」記述は簡潔な参考

---

## 関連

- 決定的/非決定的の分離 → [[02_Jev Engineering 決定と生成を分離するSystem One]]・[[07_Harness Engineering 壊れないAIエージェントの作り方]]
- state=共有メモリ → [[08_LangGraph Academy エージェント構築のコース]]・[[03_ccc関連事例調査 ボルト内の同じアプローチ]]
- join/diamond → [[05_Graph Engineering 入門 What It Is]]
- dynamic+深さ制限 → [[03_Graph Engineering with Claude 14-Step roadmap]]
- Google 系ワークショップ系列 → [[15_OmniAppワークショップ マルチモーダルAIエージェント]]・[[10_マルチエージェントでナレッジグラフ構築 Neo4j×Google ADK]]
- LangGraph ワークショップ（対） → [[05_LangGraphワークショップ 信頼性のあるエージェントの構築とテスト]]
