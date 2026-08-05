---
title: "91_Thoric講演 Fableフィールドガイド unhobblingとunknowns（出典）"
tags: [raw-source]
source: Claude Code ミートアップ講演（Fable ローンチ告知同期）

speaker: Thoric（Dropac on Claude Code @ Anthropic）
created: 2026-08-06
language: 英語音声→日本語要約（主題別再構成）
---

# 出典メタデータ

- 話者: Thoric（Dropac on Claude Code @ Anthropic）
- 形態: 講演の音声書き起こし（Fable ロールアウト告知と同時期）
- 主題: Fable のフィールドガイド・unhobbling Claude と yourself・unknowns の発見・grief・being unreasonable
- 取得: ユーザーが `tc/output/20260806_073200_transcription.txt` として提供

要約は [[11_Thoric講演 Fableフィールドガイド unhobblingとunknowns]] を参照。

> [!note] 本文について
> 本出典は音声書き起こしで、一部ノイズ・誤認識（「Dropac」は正しく採取・「3.5 new open」は「3.5 Sonnet/New Opus」、「sweeved」は「SWE-bench」、「on OpenAI」は「Opus」の誤認識の可能性等）を含む。下記は主題別に再構成した日本語訳。

---

# 講演内容（主題別再構成・日本語）

## 導入 — Fable は「マップが開く」瞬間

Fable は本日ロールアウト。RPG でチュートリアルが終わり**オープンワールドが始まる**感覚 — できることが多いが、同時に威圧的で混乱する。本講演は Fable との新しい向き合い方の**フィールドガイド**。4部構成: **unhobbling Claude / finding unknowns / dealing with the grief / being unreasonable**。

## 1. Unhobbling Claude（モデルは設計でなく育つ）

> **「モデルは設計（designed）でなく育つ（grown）」** — データ・フィードバック・計算を与え有機的に育てる。何がモデルを束縛するかは **私たちの理解**（=ハーネスとプロンプトは Claude への理解の関数）。unhobbling とは「Claude をより深く理解して解き放つ」こと。

### Capability overhang（能力のオーバーハング）
Claude は**スパイク状に賢くなる**。例: 「名前が AW で終わるポケモンは?」（1000匹中2匹: Crokna と Dreadna）。通常チャットモデルは答えられないが、**Claude Code は全ポケモンを取得して AW でフィルタするスクリプトを書いて見つける**。コード実行ツールを与えるとできる — これが capability overhang。

### モデルの進化とハーネルスの変遷
- **3.5 Sonnet 以前**: 小さなシステムプロンプト・少数ツール・多くの例
- **中期**: より多くの情報と指示を与えられるようになった（大きなプロンプト・多くのツール・多くの例）
- **Fable 級（新）**: **より小さなシステムプロンプトを求める**。例はモデルを制約する（例より想像的だから）。「do not do this」を避ける（前モデルに必要だったが今は不要）
- **Claude Code はシステムプロンプトの80%を削除**

### 質問能力の進化
- Opus 4.5 以前: ぎこちない質問
- Opus 4.5: 「スペックについて40問して」でインタビュー開始
- **Fable**: 質問を埋め込んだ HTML レポート全体を構築 — 全く新しい相互作用

### Markdown → HTML の進化
Markdown 出力 → Plan モードで理解を共有 → **Fable は深い HTML レポートを構築**

> **「これは物理学でなく生物学に近い」** — 非常に経験的・有機的。全てのルールは分かっていないが、直感と科学がある。推奨論文: "Biology of a Large Language Model"

## 2. Finding Unknowns（unknowns を発見する — unhobbling yourself）

> **「地図は領土でない（the map is not the territory）」** — 私の頭の中の plan/prompt/spec は地図。実際のコードベース・現実・制約が領土。Claude が領土で地図にないものに出会うと**unknown**（私が指定していない決定点）になる。Fable は領土を広く走査するので、unknowns を見つけないと大量に遭遇する。

### Known/Unknown マトリクス
- **Known knowns**: 自明で書かない。「見れば分かる」
- **Unknown knowns**: 知っているが明示していない
- **Known unknowns**: 知らないが認識している
- **Unknown unknowns**: 全く考慮していない。**これを知ればプロンプトが変わるもの**

### Fable で unknowns を発見する6つの方法

1. **Blind spot pass（盲点パス）**: 「このコードベースで新しい auth プロバイダについて盲点パスをして関連 unknowns を見つけ手伝って」— モジュールを走査し gotcha を特定
2. **Brainstorming prototypes（ブレスト・プロトタイプ）**: 「ダッシュボードを作って。視覚的センスはない。全く異なる設計決定の HTML を」。**言葉で説明できないもの（unknown unknowns）を反応から見つける**
3. **Interviews（インタビュー）**: 「Claude に私をインタビューさせる」。「アーキテクチャを変える質問を優先」が極めて有効
4. **References（参照）**: 別の地図を与える。「このコードを読んで理解し、それを出発点に」— HTML マークアップを React コンポーネントの地図として等
5. **Implementation notes（実装ノート）**: Fable 実行中に unknown に遭遇したら**ログに記録させる**。乖離が起きた場所と理由を把握
6. **Quiz me（クイズ）**: Fable に「何が起きたか」をクイズさせる。PR 作成・マージ時に自分が理解していることを確認。**ループ内に留まる**ことが Fable で最も重要

## 3. Dealing with the Grief（喪失への対処）

Fable を初めて使った時、**大きな獲得感と同時に喪失感**を感じた。

- LLM 以前のコーディングは「異国」のよう。30人の YC スタートアップを運営し、コードの困難さから常にトレードオフを強いられた
- 手書きコードの快感（コードベースを心に抱いて回転する感覚）と同時に、徹夜のデバッグ・週単位の停滞・「失敗の中で泳ぐ」感覚
- ほとんどのプロジェクトは失敗し、ほとんどのスタートアップは破綻する
- プログラミングは極めて困難だった

> **「唯一の抜け道は通り抜けること（the only way out is through）」** — まだ学ぶことは多いが、懸命に努力しループ内に留まり解きほぐせば、向こう側により多くのものを持って出られる。

## 4. Being Unreasonable（不合理であれ）

> **「トレードオフは実在しない」**（Anthropic の文化） — 「合理的」であると優先順位リストを書き妥協する。だが**全てをやったらどうなるか**。現実にトレードオフを示させる。

- **good, fast, cheap → pick three**（Fable の数学がトレードオフ観を変える）
- より野心的な仕事をするには、**自分自身をより野心的にする**
- **「エージェントが機能することを証明する唯一の方法は、これまでで最高の仕事をかつてない速さでやること」**
- 例: このデッキを Fable で4時間で作った
- 今年の決意: **より生産的だがより少なく働き、大切な人とより多く時間を過ごす**
- **「作る（building）のは簡単になったが、価値を生む（generating value）のは依然難しい」** — プロセスやセットアップにこだわるのでなく、価値を生むことが目的。何度も振る必要がある
