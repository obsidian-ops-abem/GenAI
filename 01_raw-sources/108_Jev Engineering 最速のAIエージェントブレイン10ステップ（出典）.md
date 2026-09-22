---
title: "108_Jev Engineering 最速のAIエージェントブレイン10ステップ（出典）"
tags: [raw-source]
source: https://x.com/0xMovez/status/2101007482919227841
author: Movez（@0xMovez）
published: 2026-09-18
created: 2026-09-20
---

# 出典メタデータ

- URL: https://x.com/0xMovez/status/2101007482919227841
- 著者: Movez（@0xMovez）
- 公開: 2026年9月18日
- 形態: X 長文ポスト（10ステップ・セットアップガイド・コード例付き）
- タイトル: **Jev Engineering: how to build the fastest AI Agent Brain in 10 Steps (Full-Setup)**
- 対象: Jev by TypeSafe AI（@typesafeai）— System One 決定特化モデル

要約は [[05_Jev Engineering 決定と生成を分離するSystem One]] を参照。

> [!note] 本文について
> Jev（TypeSafe AI）のセットアップ解説。プロモーション色を含むが、System One / System Two 分離・ハーネス2層・決定の型（Choice/Score/Noul）等の設計知見は汎用的。

---

# 原文（@0xMovez の X ポスト全文・構造化）

Every agent you've built has the same problem. An LLM that costs $0.03 per call sits in a loop answering yes-or-no questions, picking the next worker, and scoring relevance.

Those decisions don't need generation. They need a model that was built to decide.

This is the 10-step setup that gives your agents a dedicated decision brain. Install it once. Measure it. Then replace every expensive fork.

## The Jevons Paradox

1865年の法則: 蒸気機関が石炭を効率的に使うと、総石炭消費は減らず増える。AI の世界的問題と同じ — トークンは毎四半期安くなるが使用量が爆発し、請求額は同じか増える。**Jev はこのサイクルを壊すために作られた**。

## Jev とは — System One モデル

- 状態と定義済み質問を送ると、**型付きの回答と確率を返す**。テキスト生成なし・チャットなし・自己回帰ループなし
- **1つのことだけをする。決める。** LLM が同じ仕事をするより **200倍速く・400倍安い**

## 01. Split — 決定を見つける（テキストでない）

分割は単純: **操作がテキストを作るなら LLM。選択肢からの選択・値のスコア・yes/no なら Jev**。
- 例「3つのAIエージェントツールを調査して明日のブリーフィングを起草」→ 決定: ソース十分か？次のワーカーは？ドラフトはレビューreadyか？
- 取得・執筆・保存はツールと生成モデルのまま。「10アクション後に停止」等の正確なルールはコード

## 02. Playground — コード前に1問テスト

TypeSafe Playground で状態+質問（「次にどのワーカーが動くべき?」）+3オプション（research/write/review）を定義して実行。completed_work を実際の調査メモに差し替えて決定を比較。

## 03. SDK — インストールと API 接続

Python 3.12+・`pip install typesafe-sdk`。Node なら `npx skills add typesafe-ai/skills --skill typesafe-ai`（公式スキル・統合指示を提供）。

## 04. Handoff — 決定をローカル JSON キューに保存

`chief.py` = スタンドアロンの決定ルーター。ジョブを入力 → Jev が宛先を選択 → ハンドオフをローカルに保存。

```python
with TypeSafeClient(model="jev-1.13.0") as client:
    result = client.system_one(
        state=state,
        questions={
            "next_worker": Choice(
                instructions="Choose the next step for a research briefing.",
                criteria={
                    "research": "Collect evidence still needed for the goal.",
                    "write": "Draft the briefing from sufficient evidence.",
                    "review": "Goal unclear, outside scope, or work complete.",
                },
            )
        },
    )
answer = result.choices["next_worker"]
destination = "review"
if answer.choice in {"research", "write"} and answer.confidence >= 0.85:
    destination = answer.choice
# queue/research|write|review/<uuid>.json へ保存
```

- 各実行は `queue/research`、`queue/write`、`queue/review` 配下に新 JSON ファイル。**ローカルタスクキュー。保存されたジョブはワーカーの消費を待つ**
- **confidence 閾値は 0.85**。ラベル付き例で調整。**confidence は精度の割合でない**

## 05. Questions — Choice / Score / Noul

3つの質問型（異なる決定の種類向け）:
- **Choice**: 選択肢から選ぶ
- **Score**: ラベル付きでスコア
- **Noul**: yes/no（例: safe_to_run — 人間のレビューなしで安全に実行できるか）

重要な詳細:
- **Jev は質問 ID を見ない**。`safe_to_publish` というフィールド名は指示に寄与しない。実際の要件を質問に書き、各オプションを明確に記述
- **証拠を供給**。「リサーチャーが完了した」より、ソース・発見・残ギャップ。元リクエストとは別フィールドに
- **System One は全質問を並行評価**。質問を追加しても応答時間はほぼ不変・追加質問のトークンのみ課金

## 06. Dynamic Menu — 毎ターン選択肢を再構築

ブラウザの利用可能アクションはクリック毎に変わる。Browser Use は観測したコントロールの新しいリストを構築し、Jev に選ばせる。入力フィールドの記入にのみ小型 LLM がテキスト生成。
- Chief of Staff にも適用: **今存在し利用可能なワーカーから選択肢を構築**。ソース ID を含める
- **ツールが状態を変えたら選択肢をリフレッシュ**。さもなければ「昨日のメニュー」から選ばせる

## 07. Parallel — 1回の呼び出しで質問をバッチ

ディスパッチャがワーカー・緊急度スコア・承認チェックを必要とするなら、同じ状態を参照できる3つを**一緒に送る**。
- 並行質問と**投機的分岐**（可能な次アクションについて尋ね、選択された分岐に関連する回答のみ使用）をサポート
- **質問は互いの回答を読めない**。決定が新しい検索結果を必要とするなら先に検索を実行
- Browser Use の別ボトルネック: 最適化ランタイムでブラウザプロトコル呼び出し中央値を 1,092 → 101 に削減・タスク時間25%短縮（同じモデル）。**高速なモデルに払う前に繰り返しツール呼び出しを検査**

## 08. Guardrails — 制限と停止条件

- 朝ブリーフィング: ソース収集とドラフト作成を許可し、レビューで停止。**公開は別の権限チェックを要求**
- アクション制限・支出制限・進捗保存。割込み後は**最後に完了したアクションを検査してから繰り返す**。**自信ある回答はファイルが保存された/メッセージが送られたことを証明できない**
- Browser Use は Jev が DONE を選択した後、**独立に結果をチェック**。この分離を自分の完了チェックに借用

## DEEP DIVE // THE JEV HARNESS（核心）

エージェントはループで動く: LLM が何をすべきか決め → ツールが実行し → モデルが結果を評価し → タスク完了まで続く。2つのプリミティブ（tool calling と structured outputs）で構造化された。

**だが、そのループ内の全決定はいまだにフルモデル呼び出しのコストがかかる。そこでモデルよりハーネスが重要になる。**

> **ハーネスは同じモデルで78%と42%の差を作る。同じ重み。異なるループ工学。ハーネスが性能を決める。**

Claude Code・Codex・Cursor 等のコーディングハーネスは、危険なアクションを分類する仕組みを出荷済み。その分類器ステップがエージェントへの信頼を構築してきた。**だがこれまで閉源部分に閉じ込められていた。安く高性能な分類器モデルが今存在するので、同じパターンを全エージェントに採用できる**。

LangChain の `AutoModeMiddleware` は Jev を使い、ツール実行前に各ツール呼び出しの危険な決定をチェック。**ミドルウェア1行。安全チェックに生成トークンゼロ**。

```python
from langchain.agents import create_agent
from langchain_typesafe.experimental.middleware import AutoModeMiddleware

guardrail = AutoModeMiddleware(tools=["bash"])
agent = create_agent("openai:gpt-5.6-luna", middleware=[guardrail])
```

**ハーネスの2層**（どちらもテキストを生成しない・体感レイテンシを追加しない・同じ $0.042/M 価格）:
1. **モデルルーター（頂点）**: リクエストを処理できる最安モデルを選択
2. **Auto Mode ゲート（底部）**: 危険なツール呼び出しを実行前にブロック

## 09. Cost — $0.042/百万の意味

- Jev 1.13: **入力 $0.042/百万トークン・出力トークン課金なし**（新アーキテクチャでは課金計測不能なほど安い）
- 決定1,000入力トークンなら 10,000決定で $0.42
- フライトデモ $0.0039（90,558 Jev 入力トークン+テキストヘルパー）。**フライト結果を見つけるがチケットは予約しない**
- Vercel fx チーム: GPT-5.6-Luna 比で安全分類が約5-18倍高速・精度も向上（分類器の比較で全体実行時間でない）
- **完了タスク毎の請求を追跡**。安い決定がワーカーを誤った分岐へ送れば、決定自体より高くつく

## 10. Deploy — 5つの本番ユースケース

1. **ブラウザ制御**: Browser Use + Jev。7秒・$0.0039 でフライト検索。ステップ毎に新しいアクション空間・DOM状態空間・小型LLMフォールバックによるタイピング
2. **研究分類**: Hassan が1,018本のAI研究論文を分類。全体コスト $0.08・1論文あたり中央値 256ms
3. **受信箱トリアージ**: Riley Brown が500通のメールを分類。3.5セント。各メールを状態として渡し、reply/research/wait/review を Choice で
4. **モデル間タスクルーティング**: LangChain が Jev で安い/高性能モデルを選択。単純タスク→高速モデル・複雑タスク→推論モデル
5. **インスタント・コンテキスト圧縮**: Tamara。2026年になっても compaction が要約プロンプトのままなのはなぜか。**Jev が全ツール呼び出しをスコアリングし無関係なものをドロップ**。Alex Volkov テスト: ほぼ1Mトークンの Claude セッションを **1秒で 86K に圧縮**。要約パスでなく Jev 速度の関連性フィルタ

## Conclusion

Jev はチャットボットでない。**システムの現在状態を読み、定義した選択肢間で選ぶ高速決定層**。

> 真のアルファは7秒フライトデモや $0.08 分類でない。**エージェント内の高価な LLM 呼び出しのどれだけ多くが、生成を必要としなかったかに気づくこと**:
>
> **LLM に調査・計画・執筆をさせる。Jev にルーティング・スコア・承認・エスカレーションをさせる。コードに決定を実行させる。**
>
> その分割がエージェントスタック全体を変える。

1つの繰り返し決定から始める。測る。次を置き換える。大半のビルダーは全ての yes/no・ルート・スコアにフロンティアモデルのトークンを使い続ける。**思考と決定を分離した少数が、桁違いに安く速いエージェントを構築する**。
