---
title: "05_Jev Engineering 決定と生成を分離するSystem One"
tags: [summary, ai, agent-design, system-one, decision-model, routing, cost, harness]
source: https://x.com/0xMovez/status/2101007482919227841
author: Movez（@0xMovez）
published: 2026-09-18
created: 2026-09-20
---

# Jev Engineering — 決定と生成を分離する System One

> **全てのエージェントに同じ問題: $0.03/呼び出しの LLM が yes/no・次ワーカー選択・関連性スコアに使われている。それらの決定は生成を必要としない。Jev（TypeSafe AI）は System One 決定特化モデル — 状態と定義済み質問から型付き回答+確率を返し、200倍速・400倍安。「LLM に調査・計画・執筆。Jev にルート・スコア・承認・エスカレーション。コードに実行」。**

出典: [[108_Jev Engineering 最速のAIエージェントブレイン10ステップ（出典）]]（@0xMovez, 2026-09-18。本文はクリップ全文を使用。Jev by TypeSafe AI のセットアップガイド・プロモーション色を含むが設計知見は汎用）

---

## 一行で

@0xMovez による Jev（System One 決定特化モデル）の10ステップ・セットアップガイド。Jevons のパラドックス（効率化で総消費が増える）を breaks するため、「テキストを作る操作は LLM・選択/スコア/yes-no は Jev」と分離する。ハーネス2層（モデルルーター+Auto Mode ゲート）と3質問型（Choice/Score/Noul）が核心。

## 核心 — 思考と決定の分離

| 操作の種類 | 担うもの |
|---|---|
| テキストを作る（調査・計画・執筆） | **LLM** |
| 選択肢から選ぶ・値をスコア・yes/no（ルート・承認・エスカレーション） | **Jev（System One）** |
| 正確なルール（10アクション後に停止等） | **コード** |

- Jev は状態+定義済み質問 → **型付き回答+確率**。テキスト生成なし・チャットなし・自己回帰ループなし
- **200倍速・400倍安**。$0.042/百万入力トークン・出力課金なし

## Jevons のパラドックス（動機）

1865年: 蒸気機関の効率化で石炭総消費は増えた。AI も同じ — トークンは安くなるが使用量が爆発し請求額は増える。**Jev は「生成不要な決定」を切り離すことでこのサイクルを壊す**。

## 3つの質問型

| 型 | 用途 | 例 |
|---|---|---|
| **Choice** | 選択肢から選ぶ | 次のワーカー（research/write/review） |
| **Score** | ラベル付きスコア | 緊急度（low/medium/high/critical） |
| **Noul** | yes/no | safe_to_run（人間レビューなしで実行安全か） |

重要な詳細:
- **Jev は質問 ID を見ない** — フィールド名でなく質問本文に要件を書く
- **証拠を供給**（「リサーチャー完了」より ソース・発見・残ギャップ）
- **全質問を並行評価** — 質問追加で応答時間ほぼ不変
- **質問は互いの回答を読めない** — 新情報が必要なら先にツール実行
- **confidence は精度の割合でない** — 閾値（0.85等）はラベル付き例で調整

## DEEP DIVE — ハーネスが性能を決める（最重要）

> **「ハーネスは同じモデルで78%と42%の差を作る。同じ重み。異なるループ工学。ハーネスが性能を決める」**

- エージェントループ内の全決定がフルモデル呼び出しのコストになっていた
- Claude Code・Codex・Cursor は危険アクション分類を既に出荷 — **だが閉源部分に閉じ込められていた**。安く高性能な分類器（Jev）で同じパターンを全エージェントに
- LangChain `AutoModeMiddleware`: ツール実行前に各ツール呼び出しの危険決定をチェック。**ミドルウェア1行・安全チェックに生成トークンゼロ**

### ハーネスの2層（どちらも生成なし・体感レイテンシなし）
1. **モデルルーター（頂点）**: リクエストを処理できる最安モデルを選択
2. **Auto Mode ゲート（底部）**: 危険なツール呼び出しを実行前にブロック

## Guardrails の原則

- 公開は**別の権限チェック**で承認の後
- アクション制限・支出制限・進捗保存。割込み後は最終完了アクションを検査してから繰り返す
- **「自信ある回答はファイルが保存されたことを証明できない」** — Browser Use は Jev が DONE 選択後、**独立に結果をチェック**

## 5つの本番ユースケース

| # | ユースケース | 実績 |
|---|---|---|
| 1 | ブラウザ制御（Browser Use + Jev） | 7秒・$0.0039 でフライト検索 |
| 2 | 研究分類 | 1,018論文・$0.08・1論文256ms |
| 3 | 受信箱トリアージ | 500通・3.5セント |
| 4 | モデル間ルーティング | LangChain が安い/高性能モデルを選択 |
| 5 | **インスタント・コンテキスト圧縮** | ほぼ1M→86K トークンを **1秒**（要約でなく関連性フィルタ） |

## 設計原則のエッセンス

- **Dynamic Menu**: ツールが状態を変えたら選択肢をリフレッシュ（「昨日のメニュー」から選ばせない）
- **Parallel + 投機的分岐**: 可能な次アクションを尋ね、選択された分岐の回答のみ使用
- **繰り返しツール呼び出しを検査してから高速モデルに払う**（Browser Use はプロトコル呼び出し 1,092→101 で25%短縮・同じモデル）
- **完了タスク毎の請求を追跡**（安い決定が誤分岐へ送れば逆に高くつく）

---

## 本ボルト内の位置付け

- **「決定と生成の分離」** は [[04_Claudeはorchestrator専念 hook強制の分業]]（Claude orchestrator=判断・別モデル worker=実装）の**決定特化版**。@cursorvers は「判断と実装を同じモデルに握らせない」、Jev は「生成と決定を同じモデルに握らせない」— 同じ自己採点/コスト問題の別解
- **「ハーネスが同じモデルで78%と42%の差」** は [[01_LOOP vs GRAPH vs HARNESS ENGINEERING]]・[[04_Agent Harness vs Loop vs Graph Engineering]]（Harness は基盤レイヤー・必須）・[[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]（ハーネス=Claude への理解の関数）と直結。**ループ工学（loop engineering）が性能を決める**の具体数値
- **「危険アクション分類器が閉源に閉じ込められていた」** は [[05_Claude Codeの6層アーキテクチャ ダムループ]]（入力層の権限ゲーティング・YAML信頼ティア）の開放。Auto Mode ゲート = 入力層の権限ゲート
- **「kill switch/権限はコードで・安全チェックに生成トークンゼロ」** は [[06_自己改善型AIトレード機械 6段階ループと未解決の6番目]]（kill switch はコード・プロンプトでない）・[[01_エージェントファクトリの作り方 ビルダーズガイド]]（権限はモデル外で強制）と同根。**決定をプロンプトでなく構造化された型で**
- **モデルルーティング（最安モデルを選択）** は [[02_1チャットをエージェントチームへ Opus5 12ステップ]]（役割別モデル/effort ルーティング）・[[07_Everything Fable 5 Mythosクラスとプロンプトガイド]]（スキップ基準・effort ダイヤル）の自動化版
- **「インスタント・コンパクション（1M→86K・1秒）」** は [[06_Context Engineering Claude Codeの文脈設計]]（削除優先・段階的開示）・[[05_Claude Codeの6層アーキテクチャ ダムループ]]（95%到達で要約でなく構造化抽出・pruning beats summarizing）の究極形。**要約でなく関連性フィルタ**
- **「自信ある回答は実行を証明しない・独立に結果チェック」** は [[01_Agent Harness vs Loop vs Graph Engineering]]（Do not loop on confidence. Loop on evidence）・ccc の査（実機検証）・[[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]（dreaming が transcript+ツールコールを審査）と同根
- **Jevons のパラドックス** は [[10_Memory Engineering 最も見過ごされる層]]「write policy beats bigger windows」と同じ「リソース拡大でなく設計で対処」思想
- **System One / System Two**（Kahneman）のエージェント適用は [[06_AIエージェントの正体はプロンプトだった]]（エージェント=構造化プロンプト）の次の段階 — 速い直感的決定（System One）と遅い熟慮生成（System Two）の物理的分離

---

## 関連

- 判断と実装の分離（自己採点回避） → [[04_Claudeはorchestrator専念 hook強制の分業]]
- ハーネスが性能を決める → [[01_LOOP vs GRAPH vs HARNESS ENGINEERING]]
- 権限ゲート・入力層 → [[05_Claude Codeの6層アーキテクチャ ダムループ]]
- kill switch はコードで → [[06_自己改善型AIトレード機械 6段階ループと未解決の6番目]]
- 権限はモデル外で強制 → [[01_エージェントファクトリの作り方 ビルダーズガイド]]
- モデル/effort ルーティング → [[02_1チャットをエージェントチームへ Opus5 12ステップ]]
- コンテキスト圧縮（pruning beats summarizing） → [[06_Context Engineering Claude Codeの文脈設計]]
- 証拠で止まる（自信でなく） → [[04_Agent Harness vs Loop vs Graph Engineering]]
- write policy beats bigger windows → [[10_Memory Engineering 最も見過ごされる層]]
- dreaming（ツールコール審査） → [[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]
