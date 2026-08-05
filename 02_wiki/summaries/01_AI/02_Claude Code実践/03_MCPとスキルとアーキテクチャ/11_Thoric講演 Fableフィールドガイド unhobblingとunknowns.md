---
title: "11_Thoric講演 Fableフィールドガイド unhobblingとunknowns"
tags: [summary, ai, claude-code, fable, unhobbling, unknowns, harness]
speaker: Thoric（Dropac on Claude Code @ Anthropic）
created: 2026-08-06
---

# Thoric 講演 — Fable フィールドガイド：unhobbling と unknowns

> **モデルは設計でなく育つ（grown, not designed）。ハーネスは私たちの Claude への理解の関数。unhobbling = Claude を深く理解して解き放つ。Fable 級は「小さなプロンプト・少数の例」を求め、例は制約になる。「地図は領土でない」— unknowns（見えない決定点）を Fable で発見する6つの方法。そして「トレードオフは実在しない・不合理であれ」。**

出典: [[91_Thoric講演 Fableフィールドガイド unhobblingとunknowns（出典）]]（Thoric / Dropac on Claude Code @ Anthropic。本文はユーザー提供トランスクリプト tc/output/20260806_073200 から主題別に再構成）

---

## 一行で

Thoric（Dropac on Claude Code）による Fable ロールアウト同期の講演。4部構成: unhobbling Claude（capability overhang・システムプロンプトの進化）・finding unknowns（地図と領土・6つの発見手法）・dealing with the grief（LLM 以前のコーディングの困難と喪失感）・being unreasonable（トレードオフは実在しない・good fast cheap pick three）。

## 1. Unhobbling Claude — モデルは育つ

> **「モデルは設計（designed）でなく育つ（grown）」** — 有機的。何がモデルを束縛するかは**私たちの理解**。ハーネスとプロンプトは Claude への理解の関数。

### Capability overhang（能力のスパイク状向上）
- Claude はスパイク状に賢くなる
- 例: AW で終わるポケモン（1000匹中2匹）。通常チャットは答えられないが **Claude Code は全ポケモンを取得してスクリプトでフィルタ** → コード実行ツールを与えると capability overhang が解放される

### システムプロンプトの進化（3段階）
| 世代 | プロンプト | ツール | 例 |
|---|---|---|---|
| 3.5 Sonnet 以前 | 小 | 少 | 多 |
| 中期 | 大 | 多 | 多 |
| **Fable 級（新）** | **小を求める** | — | **例は制約になる（例より想像的）** |

- **Claude Code はシステムプロンプトの80%を削除**
- 「do not do this」を避ける（前モデルに必要だったが不要に）

### 質問能力と出力形式の進化
- Opus 4.5: 「スペックについて40問して」インタビュー
- **Fable**: 質問を埋め込んだ HTML レポート全体を構築
- Markdown → Plan モード → **深い HTML レポート**

> **「物理学でなく生物学に近い」** — 経験的・有機的。推奨論文: "Biology of a Large Language Model"

## 2. Finding Unknowns — unhobbling yourself

> **「地図は領土でない」** — 頭の中の plan/prompt/spec は地図。実際のコードベース・制約が領土。Claude が地図にないものに出会うと **unknown**（指定していない決定点）。Fable は領土を広く走査するので unknowns が大量発生する。

### Fable で unknowns を発見する6つの方法

| # | 手法 | 内容 |
|---|---|---|
| 1 | **Blind spot pass** | 「盲点パスをして関連 unknowns を見つけ手伝って」。モジュール走査で gotcha を特定 |
| 2 | **Brainstorming prototypes** | 「全く異なる設計の HTML を」。**言葉で説明できないもの（unknown unknowns）を反応から発見** |
| 3 | **Interviews** | Claude に自分をインタビューさせる。「アーキテクチャを変える質問を優先」 |
| 4 | **References** | 別の地図（コード）を与える。「これを読んで理解し出発点に」 |
| 5 | **Implementation notes** | unknown に遭遇したら**ログに記録させる**。乖離の場所と理由を把握 |
| 6 | **Quiz me** | Fable に「何が起きたか」をクイズさせる。**ループ内に留まる**ことが最も重要 |

## 3. Dealing with the Grief — 喪失への対処

Fable 初使用時、**獲得感と同時に喪失感**。
- LLM 以前のコーディングは「異国」。常にトレードオフに強いられた
- 手書きコードの快感と、徹夜のデバッグ・週単位の停滞・「失敗の中で泳ぐ」
- ほとんどのプロジェクトは失敗する

> **「唯一の抜け道は通り抜けること（the only way out is through）」**

## 4. Being Unreasonable — 不合理であれ

> **「トレードオフは実在しない」**（Anthropic の文化）

- **good, fast, cheap → pick three**（Fable の数学がトレードオフ観を変える）
- **「エージェントが機能することを証明する唯一の方法は、これまでで最高の仕事をかつてない速さでやること」**
- **「作るのは簡単になったが、価値を生むのは依然難しい」** — プロセスやセットアップにこだわるのでなく価値を生むことが目的

---

## 本ボルト内の位置付け

- **「unhobbling / capability overhang / product overhang」** は [[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]（Boris 講演）と同じ概念。Boris は「足場を削る（初代 Claude Code の誕生）」視点、Thoric は「モデルは育つ・私たちの理解が束縛」視点で補完。両者で unhobbling の両面（モデル側と人間側）
- **「システムプロンプトの80%削除・Fable 級は小プロンプトを求める」** は [[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]「モデル毎に80%削除・simple mode」・[[08_LangGraph Academy エージェント構築のコース]]（chain vs agent・信頼性曲線）と同根。**例が制約になる**は本ボルト運用（CLAUDE.md・Ingest 規則）にも直結 — モデルが賢くなったら CLAUDE.md の例も削除して試すべき
- **「地図は領土でない・unknowns の発見」** は [[04_Stop Vibe Coding Spec駆動開発の5ブロック]]（spec=地図・コードベース=領土・vibe coding は隠された決定）・[[05_claude-code-prompt-improver 送信瞬間に前提を補完]]（曖昧指示に質問を返す）と同思想。Thoric の6手法は spec 駆動の5ブロックを「unknowns 発見」视角で拡張
- **「blind spot pass・quiz me」** は本ボルトの Query 操作（根拠付きで回答）・Lint 操作（矛盾・孤立ページ検出）と同じ「ループ内に留まる」姿勢。Implementation notes は本ボルトの `log.md`（全操作を記録）と同構造
- **「トレードオフは実在しない・不合理であれ」** は [[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]（「本当に難しいタスクを任せる・6ヶ月前は通用しなかったが今は」）・[[07_Everything Fable 5 Mythosクラスとプロンプトガイド]]（長期多段階で真価）と同系。Fable 級モデルが可能にする「good fast cheap pick three」は本ボルトの運用効率にも直結
- **「価値を生むのは依然難しい」** は本ボルトの存在意義（知識を整理するだけでなく価値を生む）への警告。セットアップ（CLAUDE.md・3層・Lint）にこだわるのでなく、Query で実際の意思決定に使うことが目的

---

## 関連

- Boris 講演（unhobbling の両面） → [[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]
- product overhang（足場を削る） → [[04_Claudeはorchestrator専念 hook強制の分業]]
- Fable 5 / Mythos-class（長期多段階で真価） → [[07_Everything Fable 5 Mythosクラスとプロンプトガイド]]
- spec 駆動（地図と領土・unknowns） → [[04_Stop Vibe Coding Spec駆動開発の5ブロック]]
- 曖昧指示に質問を返す → [[05_claude-code-prompt-improver 送信瞬間に前提を補完]]
- 経験主義（物理学でなく生物学） → [[08_LangGraph Academy エージェント構築のコース]]
- Chain vs Agent（信頼性曲線） → [[08_LangGraph Academy エージェント構築のコース]]
