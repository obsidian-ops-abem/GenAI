---
title: "16_LangGraphワークショップ 信頼性のあるエージェントの構築とテスト"
tags: [summary, ai, langgraph, agent-design, testing, langsmith, rag, trajectory-eval]
source: https://x.com/leopardracer/status/2081361600833790109
author: LangChain チーム（ワークショップ）
created: 2026-08-03
---

# LangGraph ワークショップ —— 信頼性のあるエージェントの構築とテスト

> **「The model is not the moat.（モデルは堀でない）」** — Chain（信頼できるが柔軟でない）と React agent（柔軟だが脆い）の中間を目指す LangGraph。trajectory（ツール呼び出しの軌跡）評価で、LangGraph はローカルモデルでも一貫性を保証する一方、React は GPT-4o でも軌跡が乱れる。

出典: [[59_LangGraphワークショップ 信頼性のあるエージェントの構築とテスト（出典）]]（LangChain チーム, 2026-08-03 文字起こし。本文はユーザー提供文字起こしから再構成。※後半に ASR ループ障害あり、前半中心に構成）

> [!warning] 文字起こしの制約
> 文字起こし後半（488行以降）に ASR のループ障害（同じ文の反復）があり読み取り不能。本要約は**読み取れた前半〜中盤**を中心に構成。Q&A の一部も読み取り可能。

---

## 一行で

LangChain チームによる3時間ワークショップ。**Chain vs React agent の中間**として LangGraph を位置づけ、State/Nodes/Edges のグラフで制御フローを外だしし、Corrective RAG を具体例に構築からテスト（LangSmith）までを解説。**trajectory 評価**で、LangGraph はローカルモデルでもツール呼び出しの一貫性を保証し、React は GPT-4o でも軌跡が乱れることを実証。

## 核心: The model is not the moat（モデルは堀でない）

> LLM は常に変わる。堀とは**アプリケーション周りのシステム能力**:
> - オーケストレーションフレームワーク（LangGraph）
> - 評価基盤（LangSmith）
> - ガードレール・データフロー
>
> これらが**アプリを時間とともに改善し続ける能力**を与える。

## Chain vs Agent vs LangGraph

| | 制御フロー | 信頼性 | 柔軟性 |
|---|---|---|---|
| **Chain** | 開発者が定義（常に step1→step2） | 高い | 低い（柔軟でない） |
| **React agent** | LLM が決定（任意のツール列） | 低い（open-ended ゆえ脆い） | 極めて高い |
| **LangGraph** | グラフ（State/Nodes/Edges）で制御を外だし | 高い | 柔軟（中間） |

LangGraph の動機: **Chain と React の中間（柔軟かつ信頼できる）** を目指す。

## LangGraph の構造

| 要素 | 役割 |
|---|---|
| **State** | グラフ寿命の短期メモリ（辞書推奨）。Memory に相当 |
| **Nodes** | state を変更（ツール呼び出し等）。Tools に相当 |
| **Edges** | 次ノードを決定（LLM 判断を含む）。Planning に相当 |

> State はメッセージ履歴（スタック）でなく**辞書**がクリーン。キーでアクセスでき、node が state を取り・書き戻す。

## Tool calling の脆さ（React の問題）

- LM が正しいツールと正しいペイロードを選ぶ必要があり、**両方壊れうる**
- 5ツール、10ツールと増えると悪化。長い対話でも悪化
- → open-ended tool calling（React）は有望だが信頼性の課題

## Corrective RAG（具体例）

単純な RAG（retrieve→generate）に一步追加:
1. 取得文書が関連するか **reflect・grade**
2. 関連しなければ **web search** へ
3. 関連すれば generate

LangGraph で簡単に構築。ワークショップの notebook で実演。

## テストの3タイプ

| # | タイプ | 内容 |
|---|---|---|
| 1 | **In-app error correction** | アプリ内エラー処理（LangGraph が得意。Corrective RAG 等） |
| 2 | **Pre-production testing** | LangSmith で評価（データセット・アプリ・evaluator） |
| 3 | **Production monitoring** | LangSmith で監視・tracing |

## LangSmith 評価の核心: trajectory と end performance の両方

エージェントの評価は2軸:
- **End performance（最終回答）**: ground truth との一致
- **Trajectory（ツール呼び出しの軌跡）**: 期待する経路を通ったか

> Agent は「変な軌跡で正解に達する」ことがある。最終回答だけ見ると正解でも、軌跡は非効率・予想外。**両方を見る**必要がある。

Evaluator は LLM 判定（LLM-as-judge）またはヒューリスティック。

## 実証結果（公開データ）—— LangGraph の一貫性

| モデル | アーキ | 回答品質 | trajectory（ツール呼び出し） |
|---|---|---|---|
| ローカル Llama 8B | **LangGraph** | 低い（容量問題） | **100% 一貫** |
| Fire Function v2（Llama 7B） | **LangGraph** | 80% | **100% 一貫** |
| Fire Function v2 | React | 低下 | **劣化**（open-ended で予想外の経路） |
| GPT-4o | React | 強い | **劣化**（変な軌跡） |

**关键観察**: LangGraph はローカルモデルでも**ツール呼び出しの推論が一貫**。回答品質はモデル容量に依存するが、**アプリの推論は一貫して強い**。React はモデルが大きくても軌跡が乱れる。

## Q&A の実務知見（読み取れた部分）

### オンライン自動ユニットテスト生成
- AlphaCodeium 論文。本番では**リスキー**（LM が自動生成したテストで自動生成物をテスト＝エラー機会が増大）
- **シンプルな lightweight unit test が効果的**: ChatLangChain で import の幻覚を、import を抽出して存在チェック→エラーを LM へフィードバック。この小さな alpha で性能が大幅向上

### ルーターノード
- React agent はツール説明と全体プロンプトからツールを選ぶ（squishy）
- LangGraph はグラフ先頭に**明示的ルーターノード**を置き、常に最初に実行。信頼性向上
- ルーターにメッセージ履歴を渡せば、多回対話での経路選択も可能

### ツールが多い場合
- Open-ended tool calling は数十ツールで破綻
- **ツール説明を embedding して semantic similarity でツール選択（RAG for tools）** が有望。LM に20ツールから選ばせるより、semantic 検索の方が効果的

### RAG のチャンクサイズ（重要）
- **検索用に小さくチャンク化しつつ、生成時には全文書を渡す**（indexing と generation を decouple）
- 長文脈 LLM が安くなり、この手法が現実的
- 小さすぎるチャンクは検索には良いが recall が落ちる（文脈不足）

### Long context の loss-in-the-middle（自前調査）
- Paul Graham エッセイ120kトークンに pizza の具を隠す実験
- **事実が文書の先頭にあると recall が著しく低下**（LM は recent tokens への reasoning bias）
- 100万トークンの context stuffing は信頼できない。needle-haychart の完美な結果も信頼できない

---

## 本ボルト内の位置付け

Graph Engineering / エージェント設計の**LangChain チーム公式ワークショップ**。本ボルトの Graph 系・Loop 系ノート群に「LangGraph という具体実装」と「trajectory 評価」という新視点を加える:

- **Chain vs Agent vs LangGraph** の3層は、[[02_LOOP vs GRAPH vs HARNESS ENGINEERING]]・[[08_Agent Harness vs Loop vs Graph Engineering]] の「Harness/Loop/Graph 3層」を LangChain 実装で具体化。**Chain=開発者制御・Agent=LM制御・LangGraph=中間**という整理は本ボルトの3層議論に実装を与える
- **「The model is not the moat」** は、[[01_エージェントファクトリの作り方 ビルダーズガイド]]（工場思想・モデルでなくシステム）・[[02_24時間自走する自律型AIエージェントの設計図]]（モデルの知能でなく周囲の設計）と完全同系。ワークショップの核心メッセージ
- **trajectory 評価**（ツール呼び出しの軌跡）は、[[14_自己レビューエージェントのGraph設計 Anthropicメソッド]]（judge・ルール引用強制）・[[15_ループ仕様 論文 コーディングエージェントの実行レイヤ]]（停止ルール・検証可能目標）の「経路の正しさ」を**評価の対象**として扱う新視点。最終回答だけでなく**どう到達したか**を測る
- **Corrective RAG** は、[[02_GraphRAG 知識グラフでRAGを置き換える]]（GraphRAG）・[[05_デジタル庁 ChatGPTを業務に組み込むためのハンズオン]]（RAG 基礎）の発展形。RAG に reflect/grade/web search の自己修正を加える
- **「LangGraph はローカルモデルでも推論の一貫性を保証」** は、[[12_Graph Engineering with Claude 14-Step roadmap]]（step12 model tier・安いモデルで退屈なノード）・[[14_自己レビューエージェントのGraph設計 Anthropicメソッド]]（役割でモデル選ぶ）を**実証**。グラフ構造がモデル容量の差を吸収し推論の一貫性を保証
- **「ルーターノードを先頭に」** は、[[13_Graph Architectへの20ステップ5フェーズ]]（Step 10 router パターン）・[[12_Graph Engineering with Claude 14-Step roadmap]]（step08 runtime router）の実務ベストプラクティス。**グラフの先頭に必ず実行されるルーター**で信頼性を担保
- **「RAG for tools（ツール選択の semantic 検索）」** は、ツールが増えた時の実務解。[[02_Claude Code開発者ボリス推奨 MCPサーバー8選]]（MCP サーバー選定）・[[12_mcpo MCP-to-OpenAPIプロキシ]]（MCP の相互運用）のツール増加問題へのアプローチ
- **「indexing と generation の decouple」** は、検索用チャンクと生成用全文書を分離。RAG 設計の実務知見
- **「loss-in-the-middle・先頭の recall 低下」** は、long context の限界の実証。100万トークン万能論への警告

## 関連

- 3層（Harness/Loop/Graph）の概念 → [[02_LOOP vs GRAPH vs HARNESS ENGINEERING]]・[[08_Agent Harness vs Loop vs Graph Engineering]]
- モデルは堀でない → [[01_エージェントファクトリの作り方 ビルダーズガイド]]・[[02_24時間自走する自律型AIエージェントの設計図]]
- trajectory・経路の正しさ（verifier・停止ルール） → [[14_自己レビューエージェントのGraph設計 Anthropicメソッド]]・[[15_ループ仕様 論文 コーディングエージェントの実行レイヤ]]
- model tier・ローカルでも一貫 → [[12_Graph Engineering with Claude 14-Step roadmap]]・[[14_自己レビューエージェントのGraph設計 Anthropicメソッド]]
- router パターン → [[13_Graph Architectへの20ステップ5フェーズ]]・[[12_Graph Engineering with Claude 14-Step roadmap]]
- RAG（Corrective RAG の基礎） → [[02_GraphRAG 知識グラフでRAGを置き換える]]・[[05_デジタル庁 ChatGPTを業務に組み込むためのハンズオン]]
- ツール選定・MCP → [[02_Claude Code開発者ボリス推奨 MCPサーバー8選]]・[[12_mcpo MCP-to-OpenAPIプロキシ]]
- LangChain/LangGraph（実装） → [[03_LangChain エージェント・エンジニアリング・プラットフォーム]]
