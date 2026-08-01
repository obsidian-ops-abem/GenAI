---
title: "15_Graph Engineering 入門 What It Is"
tags: [summary, ai, agent-design, graph, claude-code]
source: https://x.com/Mahaximus_/status/2082442856417956173
author: Mahaximus (@Mahaximus_)
published: 2026-07-29
created: 2026-08-02
---

# Graph Engineering with Claude — What It Is and How to Actually Use It

> **グラフ＝「どの仕事が必要で、何が何に依存するかの地図」。既存の線形ワークフローは既にグラフ（ただし下手な）。fake-edge test で無駄な待ちを見つけ、diamond で並列化し、checker node で並列の失敗を防ぐ。Claude Code の workflow 構文で実行可能。**

出典: [[57_Graph Engineering What It Is and How to Actually Use It（出典）]]（Mahaximus @Mahaximus_, 2026-07-29。本文はクリップ全文を使用）

---

## 一行で

Graph Engineering の入門解説。node（作業単位）と edge（真の依存）の2プリミティブから、fake-edge test・diamond・checker node・static vs dynamic の判断・Claude Code の workflow 構文までを、既存ワークフローをグラフとして読み直す視点で一気通貫で説く。

## グラフとは（核心）

- **node** = 1つの作業単位（1 agent, 1 task, 1 in / 1 out）。「調べて要約してソース確認して」でなく、そのうちの1つ。小さく定義されるほど有用
- **edge** = 依存。第2ノードが本当に第1ノードの出力を必要とするときだけ繋ぐ。「順番に起こる」でなく「出力が入力を養う」時のみ
- これだけ。**node が仕事し、edge がその間を動かす。残りは全てこの2つの異なるスケール適用**

## 既存の線形ワークフローは既にグラフ（ただし下手な）

- "research → summarize → write" は単一の非分岐チェーン＝**最も単純なグラフ**。単純 ≠ 効率的
- 第1の問い: **「全ステップは本当に前を待つ必要があるか?」**。ほとんどの場合、ない
- 線形: Research→Summarize→Write→Check sources→Format→Publish（6ステップ直列、合計時間＝和）
- グラフ化: (Research + Check sources 並列) → Summarize → (Write + Format 並列) → Publish（同じ仕事、待ち減）

## fake-edge test（5分でできる）

各矢印に1問: **「このステップは本当に前ステップの結果を必要とするか?」**（「後か」でなく「使うか」）

1. 各ステップを箱で書く
2. 連続する各ペアに矢印
3. 各矢印に「AのデータはBに本当に流れるか?」
4. Yes → 実依存（保持）。No → fake edge（削除）
5. 入矢印なし = 即開始可能。出矢印なし = 最終出力

> ほぼ全ワークフローに2-3の fake edge がある。それぞれが「無料で譲っている時間」

## diamond（グラフを使う価値の核）

1ノードが複数並列ノードに fan-out → 1つの収束ノードが統合。菱形。

- 線形: search → read 1 → read 2 → read 3 → synthesize（合計時間＝和）
- diamond: search → [read 1 + read 2 + read 3 並列] → synthesize（待ち＝最遅 read の時間）
- **2ルール**: ①並列ノードは真に独立（fake edge の変装でない）②収束ノードは本当に全てを必要とする（1つで十分なら他は無駄仕事）
- diamond が出現する場所: research pipelines / code review / market analysis —「複数から集めて結合」形なら全て

## グラフが静かに失敗する場所（checker node）

並列構造が速くした自然チェックポイントを取り除くため、2つの失敗が起きる:

1. **bad node の未検出** — 3ソースの1つがゴミ（ハルシネーション/空/誤読）を返すと、それがそのまま統合ノードに入る。統合ノードは入力の1つが間違っていることを知らない。悪材料の上の自信ある回答
2. **cascade** — 線形なら即座に見える bad 出力が、収束パスでは良好出力に混ざり追跡困難に。最終ノード応答時にはダメージが希釈・不可視

**共通の修正: checker node**。並列層と収束点の間に座り、各出力を前に進める前に評価する。統合も書きもしない。「この出力は使えるか?」のみ。

checker node が捕らえる5つ:
1. empty/null 出力
2. 互いに矛盾し両立し得ない出力
3. 元タスクに対し off-topic の出力
4. 信頼不能な低 confidence シグナル
5. 統合ノードの parsing を壊す format error

> checker node のないグラフは、上流が全て動いたと仮定するグラフ。その仮定は想定より頻繁に壊れる

## static vs dynamic graph

- **static**（事前定義）— 繰り返しワークフロー・同じ構造・速く予測可能。何が・どの順で・どれくらいか分かる
- **dynamic**（実行中生長）— ノードが仕事を完了し、見つけたものを見て次ノードを決める。柔軟だが debug 困難（走った構造 ≠ 描いた構造）

判断:
1. 繰り返しで構造が同じ → static
2. 速さ・予測性が柔軟性より重要 → static
3. スコープが道中で見つかるものに依存 → dynamic
4. 一部ノードが出力に基づき次を決める必要 → dynamic
5. **常にまず static** — static が壁に当たった時だけ dynamic へ
6. 「何が走ったか正確に監査する必要」があるもの → dynamic は絶対使わない

> dynamic が必要に感じるワークフローのほとんどは、実際にはより良く設計された static で足りる

## 線形 vs グラフ（比較表）

|  | Linear | Graph |
|---|---|---|
| セットアップ時間 | 低 | 高 |
| 総実行時間 | 遅 | 速 |
| debug 容易 | はい | 難 |
| 実行中エラー処理 | 悪い | 良い（checker node） |
| 単発タスク | はい | 過剰 |
| 繰り返しワークフロー | はい | より良い |
| タスク成長への拡張 | 否 | はい |

単発で二度と繰り返さないなら線形が概ね速い。グラフが価値を持つのは**時間節約が複利で効くほどタスクが大きい**か**途中エラーが高価で checker node が元を取る**時。

## Claude Code で build する（workflow 構文）

`workflow` キーワードで Claude は順次でなく構造を parse し、依存のないノードを自動並列実行する。グラフを記述し、Claude が実行順を計算。

```python
workflow: research-and-compare
nodes:
  research_a:
    task: "Research competitor A..."
    output: competitor_a.md
  research_b: { ... }
  research_c: { ... }
  checker:
    task: "Review each research file. Flag any that are incomplete."
    depends_on: [research_a, research_b, research_c]
    output: checker_report.md
  compare:
    task: "Using the research files, write a structured comparison..."
    depends_on: [checker]
    output: comparison.md
```

**`depends_on` 行だけが全て**: 依存なし = 並列。依存あり = 待機。他は「どのノードが何を必要とするか」を決めるだけ。4つの ready-to-paste プロンプト（competitive research / multi-file code review / article research / 汎用）を掲載。

## グラフ思考への転換と CLAUDE.md

数週間使うと、タスクを「することのリスト」でなく「依存関係の集合」として読むようになる。第1問が「最初に何を?」から**「何が本当に何を待つ必要があるか?」**に変わる。答えは概ね「想定より少ない」。

プロンプトも変わる: 線形プロンプトは順に何をか伝える。**グラフプロンプトは各ピースが何を必要とするかを伝え、順序は Claude に任せる**。より小さく、編集しやすく、1ノード差し替えても壊れない。

2回以上実行する workflow には CLAUDE.md entry を追加（出力 format / checker 挙動 / エラー処理のデフォルトを1度設定）。本ボルトの運用ルール（CLAUDE.md で graph の振る舞いを固定）と完全に同構造。

---

## 本ボルト内の位置付け

- **fake-edge test と diamond** は既存 Graph 系ノートの具体的手法版。[[10_Graph Engineering 最大の間違い Loop↔Graph判断]]（Loop↔Graph判定）の「グラフ化すべきか」を通った後の、**どうグラフを設計するか**に答える
- **checker node** は [[14_自己レビューエージェントのGraph設計 Anthropicメソッド]]（judge先構築・見えない2人のレビューア）・[[12_Graph Engineering with Claude 14-Step roadmap]]（verifier 3パターン）と同系。並列の失敗を防ぐゲート層
- **static first, dynamic second** は [[10_Graph Engineering 最大の間違い Loop↔Graph判断]]（過剰グラフ警告）・[[13_Graph Architectへの20ステップ5フェーズ]]（Phase5「グラフにしない」）と同じ慎重姿勢
- **「node = 小さく定義」** は [[08_Agent Harness vs Loop vs Graph Engineering]]（Graph = nodes/edges/shared state）の実践解釈
- **CLAUDE.md で workflow デフォルト** は本ボルトの CLAUDE.md 運用と同型。Graph 設計をプロジェクト毎に固定する
- 「直線 = 退化グラフ」「次ステップは前の出力を読むか?」は [[12_Graph Engineering with Claude 14-Step roadmap]]（@0xCodez）と同じ核心。本記事は Claude Code の workflow 構文でそれを実行可能にする点が独自

---

## 関連

- Loop↔Graph 判定（グラフ化すべきか） → [[10_Graph Engineering 最大の間違い Loop↔Graph判断]]
- Graph 実装完全マニュアル（verifier/diamond/worktree） → [[12_Graph Engineering with Claude 14-Step roadmap]]
- checker node の具体実装 → [[14_自己レビューエージェントのGraph設計 Anthropicメソッド]]
- Graph Architect ロードマップ（static→dynamic含む） → [[13_Graph Architectへの20ステップ5フェーズ]]
- 3層モデル（Graph = nodes/edges/shared state） → [[08_Agent Harness vs Loop vs Graph Engineering]]
