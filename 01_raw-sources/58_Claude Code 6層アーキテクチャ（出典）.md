---
title: "58_Claude Code 6層アーキテクチャ（出典）"
tags: [raw-source]
source: https://x.com/DailyDoseOfDS_/status/2083485406780346464
author: Daily Dose of Data Science (@DailyDoseOfDS_) / Avi Chawla (@_avichawla)
published: 2026-05-02
created: 2026-08-02
---

# 出典メタデータ

- URL: https://x.com/DailyDoseOfDS_/status/2083485406780346464
- 著者: Daily Dose of Data Science（@DailyDoseOfDS_）。参照元記事は Avi Chawla（@_avichawla, 2026-05-02）
- 公開: 2026-05-02（参照元）/ 2026-08-01 クリップ
- 形態: X ポスト（日本語訳・図解付き）
- タイトル: **Claude Code の6層アーキテクチャを視覚的に解説**

要約は [[16_Claude Codeの6層アーキテクチャ ダムループ]] を参照。

> [!note] 本クリップは日本語訳ポスト。参照元の Avi Chawla 記事（@_avichawla, 2026-05-02）を @DailyDoseOfDS_ が日本語で図解解説したもの。コメント欄に実務共感（@DevCalledFede / @aditya_sarade14 / @Elev_30 / @lfrodriguesit / @MartineliAi / @ajs6888）を含む。

---

# 原文（@DailyDoseOfDS_ の X ポスト全文・日本語訳＋コメント）

Claude Codeのアーキテクチャを視覚的に解説：

（このページをブックマークしてください）

Claude Codeは、Claudeモデルを呼び出すCLIに過ぎないものではありません。

実際のシステムには6つのレイヤーがあり、モデルはそのループ内の1つのノードに過ぎません。

以下の図で各コンポーネントを説明します：

1) 入力レイヤーは、セッション管理、権限ゲーティング、YAMLベースの信頼ティアを処理し、何かがモデルに到達する前にこれらを扱います。

2) 知識レイヤーは、スキルレジストリ、コンテキストコンプレッサー、タスクグラフ、クロスセッションのメモリストアを保持します。ここが、ウェイトの外でハーネスのインテリジェンスが生きる場所です。

コンテキストコンプレッサーは、コンテキストウィンドウが約95%の容量に達したときに作動する5層のカスケードです。ChatGPTのように会話を要約するのではなく、ファイルパス、コードスニペット、エラーヒストリーに対して構造化された抽出を実行しつつ、冗長なツール出力を剪定します。目標は、コンテキストを単に小さくするのではなく、使用可能に保つことです。

3) 実行レイヤーは、bash、read、write、grep、glob、revertなどのツールごとに1つのハンドラーを備えた型付きレジストリを通じてツールディスパッチを実行します。

ストリーミングランタイムが並行実行を処理し、プロンプトキャッシュが元のコストの約10%で安定したプレフィックスを再利用します。

4) 統合レイヤーは、MCPランタイムを外部サーバー（ファイルシステム、git、カスタム）と接続します。ツールは内向きに登録され、メモリはセッション間で永続化されるMarkdownファイル（agent_memory.md）へ外向きに書き込まれます。

5) マルチエージェントレイヤーは最も過小評価されている部分で、ほとんどの人が想定するものとは全く異なって動作します。

Claude Codeは2つのレベルの並列性をサポートします：

\- サブエージェントは、セッション内で動作する軽量ワーカーです。それぞれ独自のコンテキストウィンドウを持ち、焦点を絞ったタスク（コードベースの検索、ファイルツリーの探索）を実行し、結果を親に返します。それらは互いに通信できず、独自のサブエージェントを生成することもできません。厳格な親子階層構造です。

\- エージェントチームはさらに進化します。1つのセッションがチームリーダーとして機能し、独立したチームメンバーを生成します。各メンバーは独自のコンテキストウィンドウを持つ完全なClaude Codeインスタンスとして動作します。チームリーダーはタスクをサブタスクに分解し、割り当て、進捗を監視します。

調整は2つのメカニズムを通じて行われます → 共有タスクリスト（ディスク上のJSONファイル）と、ピアツーピアメッセージングのためのメールボックスシステムです。

各チームメンバーはgit worktreeの分離を取得します。これは独自のブランチを持つ別個のワーキングディレクトリで、同じリポジトリ履歴を共有します。

これにより、エージェントはコードベースの重複部分に書き込んでもファイル競合を起こしません。完了すると、変更のないworktreeは自動的にクリーンアップされます。変更のあるworktreeは、マージ前に人間のレビュー用に永続化されます。

6) 観測可能性レイヤーはすべてをラップします。ライフサイクルフック付きのイベントバスがすべてのツールコールとメッセージをログ化し、エージェントのアクションと決定の完全な監査トレイルを作成します。

バックグラウンドエグゼキューターは、非ブロッキングでデーモンスレッドを実行するため、観測可能性がメインループを停滞させることはありません。

最後に、マスターエージェントループが6つのレイヤーの中心に位置します。コンテキストを組み立て、モデルを呼び出し、ツールリクエストを受け取り、実行し、結果をフィードバックして繰り返します。各イテレーションが1ターンです。

1ターン内で、モデルはツールコールをリクエストする可能性があります。そのリクエストは権限システムを通り、実行され、出力を次の入力としてループにフィードバックします。

ループ自体は意図的にシングルスレッドです。すべてのインテリジェンスは周囲のレイヤーにあり、ループロジックにはありません。Anthropicはこれを「ダムループ」と呼びます。なぜなら、モデルが推論し、ハーネスが仲介するからです。

これがClaude Codeの背後にあるアーキテクチャです。

Claude Sugagentsやエージェントチームについて特に深く知りたい場合、私たちはそれらについての記事を書きました。

それは、Claudeサブエージェント（分離された、fire-and-forgetワーカー）とエージェントチーム（永続的な、ピア間通信インスタンスで共有タスクリストを持つ）の違いを説明し、それぞれをいつ使用するかを解説しています。

以下で読んでください。

> **Avi Chawla @_avichawla** · 2026-05-02
>
> ![記事のカバー画像]（参照元記事）

---

## Comments

> **Federico @DevCalledFede** · 2026-08-01
>
> The compressor is the section most people will skim and shouldn't. /compact takes free-form instructions, so you can steer what gets kept before the threshold fires. And subagents run in their own window, so the big reads never touch the main one.

> **Asterix @aditya_sarade14** · 2026-08-01
>
> the "95% capacity -> structured extraction instead of summarizing" detail is the part most explainers skip
>
> learned this the hard way building my own agent tooling.. plain summarizing loses the file paths and error history that actually matter
>
> pruning beats summarizing.

> **Elev @Elev_30** · 2026-08-01
>
> layer 6 is the one nobody bookmarks, and it's the one that decides whether any of this goes near real work. a loop is only as auditable as its event bus.
>
> genuine question: does the bus log the call the model asked for and got denied at the permission gate, or only what executed?

> **Luís Rodrigues @lfrodriguesit** · 2026-08-01
>
> This is why "just use a better model" misses the point. Agent architecture is becoming a competitive advantage.

> **Martineli @MartineliAi** · 2026-08-01
>
> The "dumb loop" framing is the best part here, single threaded on purpose, with all the intelligence living in the layers around it instead of the loop logic itself.

> **安叫兽|Bird BNB @ajs6888** · 2026-08-01
>
> 这图适合先收藏再慢慢拆
