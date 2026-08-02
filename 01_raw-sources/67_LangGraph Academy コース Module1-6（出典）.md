---
title: "67_LangGraph Academy コース Module1-6（出典）"
tags: [raw-source]
source: LangGraph Academy コース（LangChain 公式）
speaker: LangChain Academy 講師（Lance）
created: 2026-08-02
language: 英語音声→日本語要約（主題別再構成）
---

# 出典メタデータ

- 出典: LangGraph Academy コース（LangChain 公式・Module 1-6）
- 講師: Lance（LangChain Academy）
- 形態: 講義動画の音声書き起こし（whisper 採取・ノイズ混入）
- 主題: LangGraph によるエージェント構築の体系的講座（state/edge/reducer/memory/HITL/deployment）
- 取得: ユーザーが `tc/output/20260802_120439_transcription.txt` として提供

要約は [[08_LangGraph Academy エージェント構築のコース]] を参照。

> [!note] 本文について
> 本出典は音声書き起こしで、ノイズ・誤認識（「React」は「ReAct」、「trust call」は「Trustcall」等）を含む。下記は主題別に再構成した日本語訳（逐語訳でなく意味構造を保った再構成）。

---

# 講義内容（主題別再構成・日本語）

## Module 1: エージェントの基礎と LangGraph core

### Chain vs Agent
- **Chain**: 開発者が固定した制御フロー（LLM呼び出しの前後に段階）。信頼性高い（同じフローが毎回走る）
- **Agent**: **LLM が定義する制御フロー**。LLM が直面する問題に応じてステップを選ぶ
- 制御の度合いを低→高にダイヤル: router（1ステップで狭い選択肢）→ 完全自律エージェント（任意のステップ列・自分でステップ生成も）

### 信頼性曲線と LangGraph の目的
- 制御を LLM に与えるほど（router→完全自律）、**信頼性は低下**する
- **LangGraph の目的: 信頼性曲線を曲げる** — 制御を押し出しても信頼性を保つエージェントを構築
- 開発者の直感と LLM 制御を組み合わせる: 固定したいステップは固定し、任意の場所に LLM を注入してエージェント化

### Graph（node と edge）
- **node**: アプリケーションのステップ（ツール呼び出し・検索ステップ等・Python 関数）
- **edge**: node 間の接続
  - **normal edge**: 常に同じ遷移（start→node1）
  - **conditional edge**: 条件で分岐（node1→node2 or node3）。関数で実装
- LangGraph の4支柱: **persistence（永続化）・streaming・human-in-the-loop・高度な制御性**

### ReAct アーキテクチャ（人気のジェネラティブエージェント）
router を一つ変更 → ReAct: ツール出力をユーザーに返すのでなく**モデルに戻すループ**。
- **Act**: モデルにツールを呼ばせる
- **Observe**: ツール出力をモデルに戻す
- **Reason**: ツール出力について推論し、次を決定（別ツール呼び出しか終了か）
- ツール呼び出しを続ける限りループ。最大再帰制限等で停止条件を追加可能

## Module 2: Memory と persistence

### Checkpoints で状態を保存
- 各ステップ後に graph state を保存（memory saver = インメモリ key-value store が最も簡単）
- チェックポイントは state のみでなく、次ノードへのメタデータ・チェックポイントID も含む
- **短期記憶**: 単一スレッド内の会話（thread_id で管理）

### 課題: 長時間会話のトークン膨張
- メッセージ履歴が長いとトークン使用量が増大（時間・コスト）
- 対策3つ:
  1. **filtering**: `remove_message` で ID 指定削除。`add_messages` reducer が認識
  2. **trimming**: トークン数でカット（`trim_messages`・`strategy="last"`・`allow_partial`）
  3. **summarization**: 長い履歴を要約で圧縮

## State schema と reducers

### Schema 定義方法（3つ）
- **TypedDict**: 辞書のキーに型。実行時に型強制なし（柔軟）
- **dataclass**: 簡潔な構文。同じく実行時強制なし
- **Pydantic**: **実行時バリデーション**（無効値でエラー）

### Reducers（状態更新の指定）
- デフォルトは**上書き**。分岐で同じキーを同時に更新すると `InvalidUpdateError`（曖昧）
- `Annotated[list, operator.add]` で reducer 指定 → リスト連結。同時更新も安全
- **add_messages reducer**: メッセージ追加・ID で上書き・`remove_message` で削除
- カスタム reducer で None 入力等のエッジケース対応

### Private state・入出力 schema
- **private state**: node 間の内部通信でユーザーに見せない値
- **input/output schema**: overall state へのフィルタ。入力は質問のみ・出力は回答のみ等

## Module 5: TaskMestro — 長期記憶エージェント

### チャットボットとの違い
- チャットボット: 常に会話を振り返りメモリを保存（固定制御フロー）
- **TaskMestro**: **いつメモリを保存するか自分で決める**（エージェント）。複数メモリ型へ保存可能

### 3つのメモリ型
1. **profile**（ユーザー情報・意味記憶）
2. **to-do collection**（タスク・意味記憶）
3. **instructions**（to-do 作成手順・**手順記憶**）

### Trustcall と Spy
- **Trustcall**: JSON スキーマでメモリを更新（JSON patching・ツール呼び出しで内部動作）
- **Spy（listener）**: Trustcall が内部で行った tool call（パッチ内容）を取り出し可視化。エージェントが「Trustcall が何をしたか」を人間へ正確に伝えられる
- `extract_tool_info` で「このドキュメントへこれらの変更・新しいメモリ作成」を整形

### memory_tool で決定を強制
- 3メモリ型のいずれかを返す typed tool を model に bind
- tool call で4方向へルーティング: update_to_do / update_instructions / update_profile / 直接応答(end)

### ツール呼び出しのクロージング
- モデルが tool call したら**対応する tool message を返す**（ツール呼び出しが実行されたことを検証）。これを怠るとモデルは待機し続ける

## Module 6: LangGraph Platform でのデプロイ

### デプロイで得られる新機能
- **agent scheduling**（スケジュール実行）
- **background runs / task scheduling**（長時間実行）
- **double texting 対策**（前のリクエスト完了前に追リクエスト）
- **agent configuration / versioning**（異なる版を同一デプロイで）

### デプロイのコンポーネント
- **graph.py**（コード）+ **langgraph.json**（CLI がデプロイ作成に必要・グラフ名とコード指定）+ **requirements.txt**
- **LangGraph CLI** で Docker イメージビルド（`langgraph build`）
- サーバーは2ワーカー: **HTTP worker**（クライアント通信）+ **Queue worker**（グラフ実行）。Redis で通信・PostgreSQL で短期/長期記憶保存
- **docker compose** で3コンテナ（server・Redis・Postgres）起動

### 開発ツール
- **LangGraph Studio**: グラフ可視化・実行・デバッグ。クラウド版も
- **LangSmith**: トレース可視化・トークン使用量・レイテンシ・ツール呼び出しフラグ

### デプロイオプション
- 無料セルフホスト（年1000ノードまで）・LangGraph Cloud（管理）・AWS EC2（データプライバシー）・セルフホスト（完全制御）
