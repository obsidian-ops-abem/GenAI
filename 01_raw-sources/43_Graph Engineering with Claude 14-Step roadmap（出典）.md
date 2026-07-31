---
title: "43_Graph Engineering with Claude 14-Step roadmap（出典）"
tags: [raw-source]
source: https://x.com/0xCodez/status/2079165300625330317
author: 0xCodez (@0xCodez)
published: 2026-07-20
created: 2026-08-01
---

# 出典メタデータ

- ポストURL: https://x.com/0xCodez/status/2079165300625330317
- 著者: 0xCodez（@0xCodez）
- 公開: 2026年7月20日
- 形態: X ポスト（長文コース・14ステップ）
- Substack: movez.substack.com
- 関連: Claude Code の **dynamic workflows**（JavaScript オーケストレーション＋サブエージェント群）

要約は [[12_Graph Engineering with Claude 14-Step roadmap]] を参照。

> [!info] 本文取得について
> 本記事はユーザーが Clippings フォルダへクリップした全文（英語）を提供したため、それに基づき転記。同著者による [[05_ループエンジニアリング14ステップ]]（Loop 版14ステップ）と対をなす Graph 版。

---

# 本文（再構成）

## 導入: 直線をグラフへ

マルチステップエージェントを作ろうとする人の多くは、**直線**（step1→step2→step3、それぞれが前の完了を礼儀正しく待つ）を作ってしまう。9割は「それらのステップの半分は待つ必要がなかった」と気づく。**ルーティングも分岐も並列化もせず、ただキューに入れる** —— 頭1つ、コンテキスト1つ、一度に1つ。窓が埋まってエージェントが何をしていたか忘れるまで。

ワークの **形そのもの**（何が何の前に走るか、何が同時に走れるか、何が全部を待たねばならないか）—— それがグラフ。ノードが思考し、エッジが結果を運ぶ。

Claude Code はこれらのグラフを直接構築するツールを提供した: **dynamic workflows**。Claude がプレーン JavaScript のオーケストレーションスクリプトを書き、サブエージェント群を spawn して実行する —— 協調自体は **コードであって会話ではないため、モデルトークンを消費しない**。

---

## 01. Nodes are jobs. Edges are what flows.

グラフは2つのものだけを持つ。**ノード** は作業の単位（1エージェント・1つの境界あるジョブ・入力1つ出力1つ）。**エッジ** は依存関係（このノードの出力があのノードの入力を養う）。

> 間違い: 「and then」をエッジとして扱うこと。「ファイルを要約して **and then** 天気を教えて」にはエッジはない —— 天気は要約を消費しない。線形スクリプトが不必要に繋いだだけの **切断された2ノード**。エッジは **データが実際に流れるときだけ** 存在する。
>
> 全ての「and then」に対して問え: **次のステップは前のステップの出力を読むか?** 読まなければエッジはなく、待ちは無駄。

## 02. Your linear script is a deggenerate graph

「A, then B, then C, then D」と書くとき、グラフを描いている —— 分岐のない単一チェーン。正しく動くが **遅く脆い**: C が止まれば D は走らず、A の作業は行き場を失う。

最初のスキルは **チェーンを描き直す** こと。各矢印に step1 の問いを立てる。ほとんどのチェーンには、データを運ばない矢印が2-3本ある（単にタイプした順序）。それらを切ると、チェーンは広い形へ崩れる —— 同時に走れる独立ノード群が、それら全部を必要とする1ノードへ養う形。

## 03. Give every node a contract

推理できないノードは並列化できない。**境界ある入力・境界ある出力・正確に1つのジョブ**。入力は明示的に渡され共有窓から推測されない。出力は定義された形（理想は validated）。

ワークフローではこの契約は **schema** で強制される。`agent()` 呼び出しに JSON schema を渡すと、spawn されたサブエージェントは validated 構造化データを返すよう強制される —— validation は tool-call レイヤーで起きるので、Claude は不一致時にリトライし、解析して祈るフリーテキストを返さない。

## 04. Treat the edge as a data contract

エッジは「B が A の後に来る」でなく、**何が渡るかの約束**。エッジを順序でなくデータで名付ければ、(a) エッジが本物か（データは実際に動くか）が即座に分かり、(b) 形が保たれる限り両端のノードを交換できる。

実践ではエッジはプレーン JavaScript。fan-out と synthesis の間の reduce（flatten, dedupe, filter）は、ノードが返した形を操作する **ただのコード**。**エージェント不要**。人々がモデルトークンを燃やすものの多くは実はエッジであり、エッジは無料。

## 05. Fan out with parallel()

N 個の独立ノードがあれば、繋がない。**parallel()** で一気に展開。Claude は thunk の配列を取り、thunk ごとに1サブエージェントを spawn し、全て並列実行、結果の配列を返す。

堅牢にする2つの詳細: (1) parallel() は **barrier** —— 全 thunk を待ってから返すので次ステージは完全なセットを見る。(2) throw した thunk はバッチ全体を reject するのでなく null に解決されるので、1つの不安定なエージェントが実行を沈めない。常に `.filter(Boolean)`。並列数はコア数で上限、超過分はキューに入るので100 thunk を渡しても全部終わる。

オーケストレーション層はコード Claude が書いたものであり、モデル会話の別ターンでないため、**トークン消費ゼロ**。Claude 自身のコンテキストは9つのソースを一度に保持しない —— 各サブエージェントが自身のコンテキストを持ち、最終答えだけが戻る。これが数十〜数百サブエージェントへスケールさせる仕組み。

## 06. Fan in at a barrier

fan-out は何かがそれを集める時にだけ有用。**fan-in** はエッジが収束するノード —— 全上流結果を一度に見て、セット全体を必要とする処理（ソース横断 dedupe、impact 順ランク、合計が空なら early-exit）。

> グラフを速く保つルール: **あるステージが真に全先行結果を一緒に必要とするときだけ barrier を使う。**
>
> リストをただ flatten する? それはエッジ、インラインでやる。臭い検査: parallel → transform → parallel と書いたとき、その中間 transform にアイテム横断の依存がないなら、pipeline を使って barrier を完全にスキップすべきだった。

## 07. The diamond: split → work → merge

fan-out と fan-in を合わせると、全シリアスなエージェントグラフの主力トポロジー **diamond** ができる。1ノードが分割、多数ノードが並列で作業、1ノードがマージ。マーケットスキャン・依存監査・コードレビュー・調査レポートの背後にある形。

正準形: **fan out → reduce → synthesize**。fan out で幅を集め、プレーンコードで reduce して圧縮、最終エージェントで synthesize して答えを書く。

> diamond を見るようになると「どうエージェントにもっとステップをさせるか」でなく「**どこで分割し、どこでマージするか**」を問うようになる —— 実際にスケールする問い。

## 08. Route the edge at runtime with a conditional

全てのグラフが固定とは限らない。あるノードの出力を検査し、どの下流パスを発火するか決める **router node**。チケットを分類して右ハンドラへ分岐、diff サイズをチェックして quick review か full audit か。

ワークフローでは、制御フローはコードにあるので **JavaScript の if/switch** に過ぎない。router の決定は Claude 駆動（サブエージェントが分類）だが、ルーティングは Claude が書いたコード —— 同じ分類には毎回同じように走る。**ノードで Claude の判断、エッジでスクリプトの信頼性**。「Claude が監査をスキップすることにした」という創発的サプライズはない。

## 09. Put a verifier on the edge

グラフの真のレバレッジはエージェントを増やすことでなく、**信頼度を生むために巻ける構造**。verifier ノードは結果が下流へ許可される前にエッジに座り、唯一のジョブは **その発見を殺そうと試みる** こと。生き残れば通す、さもなくば答えに届かない。

3つのパターン:
- **Adversarial verify**: 各発見に対し N 人の独立した懐疑家を spawn して反証を試みる。多数が生き残った時だけ保持
- **Perspective-diverse verify**: 各 verifier に異なるレンズ（correctness / security / does-it-reproduce）。多様性は N 個の同一チェックが決して捕まない失敗モードを捕まえる
- **Judge panel**: 異なる角度から N 試行を生成、parallel judges で採点、勝者から synthesize しつつ次点のベストを移植

> これが、あるチームが Bun ランタイムを **adversarial code review をループに組み込んだまま** ポートできたパターンそのもの。

## 10. Isolate nodes so one failure can't poison the graph

チェーンでは失敗は連鎖するが、グラフでは失敗は **ノードに封じられる**。parallel() 内で throw した thunk は null に解決するので、8つの良いエージェントは1つの悪いものが脱落しても返る。`.filter(Boolean)` が封じ込め。全 fan-in は完全なセットでなく欠損入力を許容するよう設計せよ。

より微妙な失敗は **ノードが互いに踏み合う** こと。エージェントが並列でファイルを書くとき衝突できる。修正は隔離: **worktree** —— 各エージェントが自身の git worktree で走りサンドボックスで作業しクリーンにマージ。ノードが本当に並列で書くときだけ手に取る。**全実行へのデフォルト税でなく、それを必要とする1つのトポロジーへのシートベルト**。

## 11. Add a cycle - but make it converge

ジョブの大きさが中に入るまで分からないとき（未知サイズの発見、1つのバグを見つけると3つ出るバグスイープ）。**cycle** —— より早いノードへ戻る制御されたエッジ。収束しない cycle は予算が尽きるまでエージェントを spawn する無限ループ。

収束するパターンは **loop-until-dry**: K 連続ラウンドが新しいものを何も出さなくなるまで finder を spawn し続け、止まる。

> ほぼ全員が初回で犯す間違い: **何に対して dedupe するか**。confirmed 結果でなく **見たもの全て（everything seen）** に対して dedupe する。さもなくば拒否された発見が毎ラウンド再出現し、ループは永遠に dry にならず、同じ袋小路を再発見するために支払う機械を築いてしまう。

## 12. Tier the models across the nodes

全ノードが最高モデルを必要としない。グラフはこれを明白にする: あるノードは境界あり反復的（フィールド抽出・チケット分類）、あるノードは真の判断（レポート synthesize・発見裁定）。**退屈なノードは安いモデル** で走らせ、高価なトークンを判断が実在する場所に使う。

ワークフローでは、spawn されるサブエージェントは **スクリプトが上書きしない限りセッションモデルを継承** する —— デフォルトでは大規模実行は全てセッションティアで請求される。`agent()` の model オプションでそのノードだけ別ルートへ。**大規模実行前に `/model` をチェック** し、fan-out の反復ノードを安いモデルへ、merge ノードを上に保つ。形に触れずにトークン飢餓グラフを高価から経済的へ変えるレバー。

## 13. Topology is your cost and latency

グラフの形は装飾でなく、**wall-clock 時間の最大のレバー**。parallel() 対 pipeline()。parallel() barrier は全てを最遅ノードに待たせてから次ステージが始まる。pipeline() は各アイテムを全ステージを独立してストリームし、barrier なし —— アイテム A がステージ3にいる間にアイテム B はまだステージ1。速いアイテムは遅いものの後ろでアイドルせず早く終わる。

> **デフォルトは pipeline()。** あるステージが真に全先行結果を一度に必要とするときだけ barrier に手を伸ばす —— セット横断 dedupe、合計の early-exit、「他の発見」と比較するプロンプト。「コードが綺麗」「ステージが分かれて感じる」は理由でない。barrier レイテンシは本物の測れる無駄時間。**separate（分離）は synchronized（同期）と同じでない。**

## 14. Let Claude draw the graph - self-routing

最後の手は、事前に計画できないジョブに対して **手でグラフを描くのを止める** こと。**dynamic workflows** で目的を記述し、Claude がオーケストレーションスクリプトを **自分で書く** —— タスクを分解、fan-out を選択、サブエージェント群を spawn、結果を synthesize。

3つの入り口: (1) プロンプトで **「workflow」** と言うと Claude がタスク用に書く。(2) 保存/バンドル済みのものを実行 —— `/deep-research` は本番出荷の実グラフ: scope → parallel search → fetch → adversarial verify → synthesize、本コースの骨格そのもの。(3) ultracode をオンにするとセッションの全実質的タスクにワークフローを計画。実行が良いとき **s を押して** `.claude/workflows/` へ保存 —— バージョン管理され、名前で再実行可能、リポを clone した誰でも起動できるグラフ。

---

## Six graphs to build with Claude this week

- **Security sweep across every route** —— route ファイルごとに1サブエージェント、missing auth を探し、verifier pass が全発見を確認
- **Cited report with /deep-research** —— 既に Claude Code 出荷済み。質問を異なる角度に分解、parallel search、ソース dedupe、3投票懐疑家で全クレームを adversarially verify
- **Port a module, file by file** —— Bun の天井を自リポへ。ファイル横断に翻訳を fan-out、各々にテストスイートをゲート、失敗をループバック、adversarial review が単一パスなら壊れて出荷されるものを捕捉
- **Adversarial review of a diff** —— diff サイズでルート: 小変更は quick pass、大変更は distinct レンズ（correctness/security/performance）の full parallel audit、judge panel が synthesize
- **Ecosystem scan on a schedule** —— 一度保存、永遠に再実行。多くのソースを parallel チェック、barrier で impact 順にランク、digest を書く。`.claude/workflows/` でバージョン管理
- **Discovery of unknown size** —— バグがいくつあるか分からない。finder を parallel、**見たもの全て** に対して各発見を dedupe、生存者を verify、2ラウンド何も出ないまでループ、停止

## Conclusion

> A prompter asks a question. An architect draws a graph.
>
> 直線エージェントは決して天井でなく、最初の形だった —— タイプする方法に合致するから全員が手に取る。**1行、1頭、一度に1つ。**
>
> ノードとエッジを見えるようになると、エージェントにもっとやらせるのを止め、グラフに広くやらせ始める: 独立な作業で fan-out、信頼度が重要な所でエッジを gate、判断が及ばない所でモデルを tier。
>
> 多くは行にステップをキューし続ける。**グラフを描くことを学んだ者は艦隊を走らせる** —— 残りが詰まっている天井に気づきもしない。
