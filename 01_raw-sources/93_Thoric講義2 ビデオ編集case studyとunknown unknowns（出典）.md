---
title: "93_Thoric講義2 ビデオ編集case studyとunknown unknowns（出典）"
tags: [raw-source]
source: 講演（Claude Code チーム・小規模イベント・ideas mid-formation）

speaker: Thoric / Starick（Mike）（Claude Code チーム @ Anthropic・元 vise founder・Goodfire で解釈可能性）
created: 2026-08-07
language: 英語音声→日本語要約（主題別再構成）
---

# 出典メタデータ

- 話者: Thoric / Starick（Mike）（Claude Code チーム @ Anthropic・在籍約1年・元 vise founder（5年運営）・Goodfire で解釈可能性から AI 入り）
- 形態: 小規模イベントでの講演（本人「7人だと思っていた・ideas mid-formation」と発言・翌週の大きな講演に向けた試論的な内容）
- 主題: AI がバリュエーションを獲得できていない・capability overhang・unhobbling Claude・ビデオ編集 case study・unknown unknowns の削減・ask_user_questions ツールの進化
- 取得: ユーザーが `tc/output/20260807_231216_transcription.txt` として提供
- 関連: [[91_Thoric講演 Fableフィールドガイド unhobblingとunknowns（出典）]]（翌日の完成版講演）・本講演はその前日の試論版で、ビデオ編集 case study と Q&A が詳細

要約は [[13_Thoric講義2 ビデオ編集case studyとunknown unknowns]] を参照。

> [!note] 本文について
> 本出典は音声書き起こしで、ノイズ・誤認識（「Starick」は「Thoric」、「Mike」は呼び名、「vise」は「Vise」、「cloud code」は「Claude Code」、「Goodfire」は正しく採取、「2003.5/2003.8」は「3.5 Sonnet/Opus」の誤認識等）を含む。下記は主題別に再構成した日本語訳。

---

# 講演内容（主題別再構成・日本語）

## 1. AI はまだバリュエーションを獲得していない

「私たちはもっと良くならなければならない」。Twitter で「トークンを何に使っているの？成長はどこ？」と聞かれるが、その通り。AI の目標は人類の GDP を意味ある形で向上させることだが、まだ示せていない。

- **ソフトウェアは超安価になった**・**知識労働も安価になった** — これは明らか
- だが**価値を生む（generating value）のは依然極めて困難**・スタートアップを立ち上げるのも困難
- ソフトウェアと知識労働はその一部でしかない
- **「私たちはまだ正直、バリュエーションを獲得していない」** — AI への投資額に見合う価値をまだ生んでいない

### 「自分自身と交渉しない（don't negotiate against ourselves）」
CEO 時代: 優先順位を書き出し「これらがどうトレードオフするか」を合理的に考える。だが**もっと不合理だったらどうか**。**現実にトレードオフを示させる**。AI/Claude では good fast cheap がまだトレードオフか？**そうではないかもしれない**。私たちのメンタルモデル（トレードオフ・野望）から自由になり、AI の約束を届ける必要がある。

## 2. モデルはスパイク状に賢くなる（直線でない）

モデルは**有機的**に育つ。データ・RL 環境・訓練を慎重に積むが、結果がどうなるかはモデルを得るまで分からない。「95% on SWE-bench」と決めるのでなく育てる。

- **直線でなく予測不可能な方向に賢くなる**
- 例: 3.5 Sonnet 時代、「コーディングをどう解くか」と考えた時、研究では「コンテキストウィンドウを1億トークンにしてコードベース全体を詰め込む」と予想した。**だが実際は tool calling・bash・grep で解いた**

## 3. Capability overhang と unhobbling Claude

### ポケモンの例（AW で終わる）
1000匹以上のポケモンのうち AW で終わるのは2匹（Crocodile と Dredna — ※ASR 誤認識の可能性、実際は Crocalor と Drednaw か）。
- ChatGPT は1匹しか見つけられない。ChatGPT に聞いて止まるユーザーは「できない」と結論する
- だが**Claude Code（コード実行ツール付き）は全ポケモンリストを取得して grep する**。1行で完了
- さらに: 任意の正規表現でポケモンを検索する **Web アプリを生成**することもできる
- **「問題が暗示する以上の豊かさ（abundance）」がある** — だが平均ユーザーは Claude を深く理解せず、より良いプロンプトやツールを与えることに気づかない

> **Opus 3.5 / Fable には信じられない capability overhang がある**。モデルができることと私たちが利用していることのミスマッチ。

### Claude の立場に立つ（empathizing with the model）
- ユーザーが「コードベースについて何か知ってる？」と聞いた時、Claude は**文脈を探る**必要がある: 技術的か？コードベースの規模は？どの程度の深さか？他に参照できる context（Git/Slack）はあるか？なぜ聞いているのか？
- 「上司に auth モジュールを説明してと言われたら『何について？』と聞くのと同じ」
- harness はこれを助けられる（memory でユーザーを学ぶ等）が、まだ Claude 側にやるべき仕事がある
- より明示的なプロンプト例: **「私は経験豊富な TypeScript エンジニア。このモジュールには未知だが、高 compute 問題なので subagent を使って」**

## 4. ビデオ編集の case study（capability overhang の具体例）

> 「Claude は動画を編集できるか？」と聞かれたらほとんどの人は「ノー」と答える。**だが Claude Code で実際にやっている**。

### プロセス
- 動画制作会社が撮影。**動画編集者を使わず Claude Code で編集**
- 生素材（クリップ群）+ transcript を Claude へ
- Claude が transcribe・最適なカットを選択・UI/オーバーレイを生成・デザインシステムに沿った artifact を生成
- **「知識労働はますます、コードとスクリプトとデータのフォルダを agent が制御するものになりつつある」**

### Color grading — unknown unknowns の具体例
第1版と第2版で色が大きく違った。原因は**color grading**（動画の色を整える技術）。
- 動画生素材は overexposed の raw フォーマット。色を引き出す「アート」がある
- **Thoric 自身は color grading の unknown unknowns だらけ**だった（知識があったのは ffmpeg・transcription・remo のみ）

### Unknown unknowns を削減するプロセス
1. Claude に color grading を説明させる → だが**レポートを読んでも理解できなかった**（よくある「glaze over」現象）
2. **可視化を頼む** — vector scope とは何か？文献を引いて
3. **ピクセル毎に値がどう変わるかの可視化**を構築
4. **color grading は本質的に shader**（ピクセルを入力し別の色を出力）というメンタルモデルを獲得
5. 重要な洞察: **肌の色は背景と別に grade する**（人間の肌色は動的範囲が狭く、紫に見えると不自然だが背景は紫でもOK）
6. 自分の肌色と背景が近かったため Claude には興味深い課題だった
7. **このプロセスで unknown unknowns を削減し、Claude へより良い指示が出せるようになった**

### なぜ unknown unknowns の削減が重要か
「より良いものをより速く ship する・GDP を駆動する・より良いプロダクトを作る」には、**unknown unknowns を削減する**ことに極めて上手にならなければならない。

## 5. ループ内に留まる手法（概要）

探索・ブレスト・インタビュー・技術計画・implementation notes・explainers（前回講演と同じ6手法）。**「高レベルのアイデア: おそらく多くの unknown unknowns を持ち、その結果ほぼ確実に十分野心的でない。これから自分を解放して Claude を un-hobble しより有用な仕事をさせるにはどうするか」**

## Q&A（詳細・前回講演にない内容）

### Q: 作業のどれくらいを Claude で？
- 探索・思考の多くを Claude へ（artifact 生成・スマホでインタビュー）
- in-the-loop コーディングは Claude Code
- **Claude Tag は開始・ジョブ管理・multiplayer に極めて有効**。Tag はデフォルトで multiplayer（組織全体・フィードバックチャンネル）

### Q: モデルへの共感と variant 設計のプロセスは？
**「今はアート（art）だ」**。科学ではない。

### ask_user_questions ツールの進化の歴史（Thoric が構築）
1. 当初: モデルが1回だけ呼べる
2. チェーン: 「インタビューして」で繋げる
3. 30-40問をまとめる
4. **現在: HTML レポートを構築し、はるかに深い回答を得る**
- 「ask_user_questions をどう使うかの進化」には capability overhang がある
- Markdown → Plan モード → **HTML レポート**。**形式を切り替える（markdown → HTML）ことで能力がアンロックされる**。これを見つける科学はない

### Q: ソフトウェア構築で agent が自分の欲しいものを構築するのに時間がかかる
**「プロトタイプを多数作る」**:
- HTML モックアップ等の**安いプロトタイプ**で agent に完全実装させず感覚を掴む
- 「自分が何を欲しいか分からない」ことが多い — 実装の深い段階で発見される。それが遅延の原因（80% 到達して「いや、これは違う」）
- **iterative: spec → interview → prototype → プロトタイプ PR → 学びを取って reset → 再開**
- 「最も価値のあるものを構築したい」

### Q: Claude から何を学ぶかをどう決める？
- 「私の主な brain rot は Claude が物事を説明すること」
- agent engineering は楽しい — ただ楽しいだけで結果を駆動していないこともある
- 「出力の何が悪いか？どう良くなるか？」→ 答えは**unknown unknowns を持っているから**。うまく表現できない何か・ユーザーについて知らないこと
- Claude は全てについて学べるが、**全てについて学ぶ必要はない。特定の必要なことについて学ぶ**
