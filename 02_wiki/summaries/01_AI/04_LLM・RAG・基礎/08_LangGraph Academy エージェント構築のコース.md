---
title: "08_LangGraph Academy エージェント構築のコース"
tags: [summary, ai, langgraph, langchain, agent, platform, memory, hitl]
source: LangGraph Academy コース（LangChain 公式・Module 1-6）
speaker: Lance（LangChain Academy）
created: 2026-08-02
---

# LangGraph Academy — エージェント構築のコース

> **Chain（固定制御）vs Agent（LLM定義制御）。制御をLLMに与えるほど信頼性は下がるが、LangGraph は「信頼性曲線を曲げる」ことを目指す。Graph（node/edge）で開発者制御とLLM制御を組み合わせ、persistence/streaming/HITL/高度な制御性の4支柱。ReAct（Act→Observe→Reason ループ）・memory（checkpoint）・Trustcall（長期記憶）・Platform（デプロイ）を6モジュールで体系化。**

出典: [[67_LangGraph Academy コース Module1-6（出典）]]（LangChain 公式コース。本文はユーザー提供トランスクリプト tc/output/20260802_120439 から主題別に再構成）

---

## 一行で

LangChain 公式の LangGraph コース（Module 1-6）。エージェントの基礎（chain vs agent・ReAct）から state/reducer/memory/HITL・長期記憶エージェント（TaskMestro）・本番デプロイ（LangGraph Platform）までを体系的に扱う。

## 1. Chain vs Agent と信頼性曲線（核心）

| | 制御フロー | 信頼性 |
|---|---|---|
| **Chain** | 開発者が固定 | 高い（毎回同じ） |
| **Agent** | **LLM が定義** | 制御を与えるほど低下 |

- 制御の度合い: router（狭い選択肢）→ 完全自律（任意ステップ・自己生成）
- **LangGraph の目的: 信頼性曲線を曲げる** — 制御を押し出しても信頼性を保つ
- 開発者制御（固定ステップ）+ LLM制御（任意の場所に注入）を組み合わせる

## 2. Graph の core 抽象（node と edge）

- **node**: ステップ（Python 関数・ツール呼び出し・検索）
- **edge**: normal（常に同じ遷移）/ **conditional**（条件で分岐・関数実装）
- **4支柱**: persistence・streaming・human-in-the-loop・高度な制御性

## 3. ReAct アーキテクチャ

router を一つ変更 → ツール出力をユーザーでなく**モデルに戻すループ**:
1. **Act**: モデルにツール呼び出し
2. **Observe**: ツール出力をモデルに戻す
3. **Reason**: 出力について推論し次を決定（別ツールか終了か）

ツール呼び出しを続ける限りループ。最大再帰制限等で停止。→ [[01_Graph Engineering エージェントを行列から解放する]]・[[04_Graph Engineering with Claude 14-Step roadmap]] の dynamic workflows と同系

## 4. State schema と reducers

### Schema 定義（3方法）
- **TypedDict**: 辞書キーに型・実行時強制なし（柔軟）
- **dataclass**: 簡潔・同じく強制なし
- **Pydantic**: **実行時バリデーション**（無効値でエラー）

### Reducers（状態更新の指定）
- デフォルトは**上書き**。分岐で同キー同時更新は `InvalidUpdateError`（曖昧さ）
- `Annotated[list, operator.add]` で連結・同時更新も安全
- **add_messages reducer**: 追加・ID 上書き・`remove_message` 削除
- private state（node 間内部通信）・input/output schema（overall state へのフィルタ）

## 5. Memory と persistence

- **短期記憶**: checkpoint で各ステップ後に state を保存（thread_id 管理）。memory saver が最も簡単
- **課題**: 長時間会話のトークン膨張
- **対策3つ**: filtering（remove_message・ID削除）・trimming（トークン数カット）・summarization（要約圧縮）

> **[[05_Claude Codeの6層アーキテクチャ ダムループ]] の「コンテキスト95%到達で要約でなく構造化抽出」**・**[[06_Context Engineering Claude Codeの文脈設計]]（削除優先・段階的開示）**と同じ「長期コンテキスト管理」課題。LangGraph は reducers/messages API で機械的に扱う

## 6. TaskMestro — 長期記憶エージェント（Module 5）

### チャットボットとの違い
- チャットボット: 常に会話を振り返り保存（固定フロー）
- **TaskMestro**: **いつ保存するか自分で決める**（エージェント）・複数メモリ型へ

### 3メモリ型
1. **profile**（ユーザー情報・意味記憶）
2. **to-do collection**（タスク・意味記憶）
3. **instructions**（to-do 作成手順・**手順記憶 procedural memory**）

### Trustcall と Spy
- **Trustcall**: JSON スキーマでメモリ更新（JSON patching・内部でツール呼び出し）
- **Spy（listener）**: Trustcall の内部 tool call を取り出し可視化。「Trustcall が何をしたか」をエージェントが人間へ正確に伝えられる（**検証器の透明性**）
- memory_tool（3型のいずれかを返す typed tool）で決定を強制・4方向へルーティング

### ツール呼び出しのクロージング
- モデルが tool call したら**対応する tool message を返す**（実行検証）。怠るとモデルは待機 → ccc の査（検証ロール）・[[02_1チャットをエージェントチームへ Opus5 12ステップ]]（検証器は仕事しなかった別役割）と通底

## 7. LangGraph Platform デプロイ（Module 6）

### デプロイで得られる新機能
- **agent scheduling**（スケジュール実行）→ [[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]（loops/routines・Claude Code 自身を保守）と同系
- **background runs**（長時間実行）→ intent-cli の停滞検知・ccc の非同期タスクと同系
- **double texting 対策**（前リクエスト完了前の追リクエスト）
- **agent configuration/versioning**（異なる版を同一デプロイで）

### コンポーネント
- graph.py + langgraph.json（CLI がデプロイ作成に必須・グラフ名とコード指定）+ requirements.txt
- サーバー: **HTTP worker**（通信）+ **Queue worker**（実行）。**Redis**（通信）+ **PostgreSQL**（短期/長期記憶）
- docker compose で3コンテナ起動

### 開発ツール
- **LangGraph Studio**: グラフ可視化・実行・デバッグ（クラウド版も）
- **LangSmith**: トレース可視化・トークン使用量・レイテンシ・ツール呼び出しフラグ

---

## 本ボルト内の位置付け

- **「Chain（固定）vs Agent（LLM定義）」「信頼性曲線を曲げる」** は [[01_LOOP vs GRAPH vs HARNESS ENGINEERING]]・[[04_Agent Harness vs Loop vs Graph Engineering]] の3層フレームと同じ視点。LangGraph は Graph 層の実装基盤
- **ReAct（Act→Observe→Reason ループ）** は [[05_ループエンジニアリング14ステップ]]・[[02_Loop Engineering Claude,GPT 実戦で効くもの]]（Loop の反復）・[[01_Agent Harness vs Loop vs Graph Engineering]]（Loop の stop rule・証拠で止まる）の具体実装
- **conditional edge・分岐・並列** は [[05_Graph Engineering 入門 What It Is]]（fake-edge test・diamond・checker node）の LangGraph 実装。reducer で「同時更新の曖昧さ」を解決する仕組みは、diamond の収束ノード問題への直接的答え
- **Trustcall + Spy（検証器の透明性）** は [[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]（judge 先構築・見えない2人のレビューア）・[[03_ccc関連事例調査 ボルト内の同じアプローチ]]（ccc の査の独立・伝言ゲーム対策）と同根。Trustcall が何をしたかを可視化する Spy は「報告は過去についての主張・実行ログが ground truth」（intent-cli v0.6.2 G556）と同じ思想
- **3メモリ型（profile/to-do/instructions）** は [[18_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]]（4層×1writer・identity.md/projects.md/tasks.md）と構造が相似。LangGraph は long-term store（namespace でメモリ型を分離）で実装
- **LangGraph Platform の background runs/scheduling** は ccc の Forgejo Actions（pushトリガー・CI/CD）・[[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]（Claude Code 自身を自律保守するルーチン）と同じ「スケジュールドエージェント」系譜
- **Pydantic による実行時バリデーション** は [[04_Stop Vibe Coding Spec駆動開発の5ブロック]]（spec の Constraints・境界を構造で守る）・[[01_Agent Harness vs Loop vs Graph Engineering]]（Harness の Safety And Governance）の具体実装

---

## 関連

- 3層フレーム（Harness/Loop/Graph） → [[04_Agent Harness vs Loop vs Graph Engineering]]
- Loop の反復・stop rule → [[02_Loop Engineering Claude,GPT 実戦で効くもの]]
- Graph 入門（conditional edge・diamond） → [[05_Graph Engineering 入門 What It Is]]
- verifier/judge（Trustcall Spy） → [[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]
- ccc の査・伝言ゲーム → [[03_ccc関連事例調査 ボルト内の同じアプローチ]]
- コンテキスト管理（filtering/trimming） → [[06_Context Engineering Claude Codeの文脈設計]]
- 第二の脳（4層×1writer） → [[18_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]]
- スケジュールドエージェント（loops/routines） → [[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]
- spec の Constraints（Pydantic バリデーション） → [[04_Stop Vibe Coding Spec駆動開発の5ブロック]]
- LangChain/Langflow（プラットフォーム系） → [[03_LangChain エージェント・エンジニアリング・プラットフォーム]]・[[04_Langflow ビジュアルAIワークフロービルダー]]
