---
title: "59_LangGraphワークショップ 信頼性のあるエージェントの構築とテスト（出典）"
tags: [raw-source]
source: https://x.com/leopardracer/status/2081361600833790109
author: LangChain チーム（ワークショップ）
created: 2026-08-03
---

# 出典メタデータ

- 形態: ワークショップ（3時間）の文字起こし
- テーマ: **信頼性のあるエージェントの構築とテスト**
- 登壇者: LangChain チーム（LangGraph / LangSmith のチーム）
- 文字起こし日: 2026-08-03
- 文字起こしファイル: [[59_LangGraphワークショップ 信頼性のあるエージェントの構築とテスト（出典）]]

要約は [[16_LangGraphワークショップ 信頼性のあるエージェントの構築とテスト]] を参照。

> [!warning] 文字起こしの品質について
> 本文字起こしは前半（〜226行付近）は比較的読めるが、**後半に ASR のループ障害**（同じ文が何十回も繰り返される）が激しくある。特に「関数呼び出し〜tool calling」の説明部分（76-105行）と、末尾（488行以降）が顕著。後半の Q&A の一部は読み取れるが、ループ部分は実質的に情報なし。本出典の要約は**前半の読み取れる部分**を中心に構成。

> [!info] 本文取得について
> ユーザーが WSL 上の文字起こしファイル（`20260803_082732_transcription.txt`）を提供。同ファイルを `01_raw-sources/59_...txt` としてコピー済み。本 .md はメタデータのみ。

---

# 文字起こしの読み取れた主要ポイント

詳細は `59_LangGraphワークショップ...txt` を参照。以下は読み取れた主要ポイントのメモ（要約ページで整理）:

## 1. Chain vs Agent

- **Chain**: 開発者がコントロールフローを定義。常に step1→step2 の順
- **Agent**: LLM がコントロールフローを決定。step1 の出力を見て戻るか進むか

## 2. Tool calling の脆さ

- LM が正しいツールと正しいペイロードを選ぶ必要があり、両方壊れうる
- 5ツール、10ツールと増えると悪化。長い対話でも悪化
- Open-ended tool calling agents は有望だが信頼性の課題

## 3. LangGraph の動機: 中間を目指す

- Chain: 信頼できるが柔軟でない
- React agent: 極めて柔軟だが信頼性に課題
- → **中間（柔軟かつ信頼できる）**を目指す

## 4. LangGraph の構造

- **State**: グラフの寿命にわたる短期メモリ（辞書）
- **Nodes**: state を変更（ツール呼び出し等）
- **Edges**: 次にどのノードへ行くか決定（LLM による判断を含む）
- Memory・Tools・Planning が共通して存在

## 5. Corrective RAG の例

- 単純な RAG（retrieve→generate）に一步追加: 取得した文書が関連するか reflect・grade
- 関連しなければ web search へ
- LangGraph で簡単に構築

## 6. テストの3タイプ

1. **In-app error correction**: アプリ内エラー処理（LangGraph が得意）
2. **Pre-production testing**: LangSmith で評価
3. **Production monitoring**: LangSmith で監視

## 7. LangSmith の評価

- データセット（ground truth QA ペア）
- アプリケーション（エージェント）
- **Evaluator**: LLM 判定 or ヒューリスティック
- エージェクトの **trajectory（ツール呼び出しの軌跡）** と **end performance（最終回答）** の両方を評価

## 8. 実証結果（公開データ）

- **LangGraph**: ローカルモデル〜7B まで **tool calling trajectory が一貫して高品質**（100%）
- **React agent**: GPT-4o でも trajectory が劣化。open-ended ゆえに予想外の経路へ
- **回答品質**: モデル容量に依存（7B<大モデル）。だが LangGraph は**推論の一貫性**を保証

## 9. "The model is not the moat"（重要）

> モデルは堀でない。LLM は常に変わる。堀とは**アプリケーション周りのシステム能力**:
> - オーケストレーションフレームワーク（LangGraph 等）
> - 評価基盤（LangSmith 等）
> - ガードレール・データフロー
>
> これらが**アプリを時間とともに改善し続ける能力**を与える。

## 10. Q&A（読み取れた部分）

- **オンライン自動ユニットテスト生成**: AlphaCodeium 論文。本番ではリスキー。シンプルな lightweight unit test が効果的（ChatLangChain の import 幻覚を、import を抽出して存在チェック→フィードバック）
- **ルーターノード**: React agent がツール選択で迷うのに対し、LangGraph はグラフ先頭に**明示的ルーターノード**を置き常に最初に実行。信頼性向上
- **State は辞書が良い**: メッセージ履歴（スタック）より、キーでアクセスできる辞書がクリーン
- **ツールが多い場合**: open-ended tool calling は数十ツールで破綻。ツール説明を embedding して**semantic similarity でツール選択（RAG for tools）**が有望
- **RAG のチャンクサイズ**: 検索用に小さくチャンク化しつつ、**生成時には全文書を渡す**（indexing と generation を decouple）。長文脈 LLM が安くなり現実的
- **Long context の loss-in-the-middle**: 自前調査で、事実が文書の**先頭**にあると recall が著しく低下（reasoning bias to recent tokens）。100万トークンの context stuffing は信頼できない

> 末尾（488行以降）は ASR ループ障害で読み取り不能。
