---
title: "37_Graph of Loops Claude Code完全システム10リポジトリ（出典）"
tags: [raw-source]
source: https://x.com/Granite0x/status/2080665298609328201
author: Granite0x (@Granite0x)
published: 2026-07-24
created: 2026-08-01
---

# 出典メタデータ

- ポストURL: https://x.com/Granite0x/status/2080665298609328201
- 著者: Granite0x（@Granite0x）
- 公開: 2026年7月24日
- 形態: X 長文ポスト（10リポジトリ解説）
- タイトル: **A Graph of Loops: Build a Full Claude Code Agent System From GitHub - One Repo Per Step**

要約は [[11_Graph of Loops Claude Code完全システム10リポジトリ]] を参照。

> [!info] 本文取得について
> 本記事はユーザーが Clippings フォルダへクリップした全文（全編・英語）を提供したため、それに基づき転記・再構成。星数・ライセンスは GitHub API から取得（公開時点）。

---

# 本文（再構成・英語原文保持）

## 核心テーゼ

> **Every node in the graph is an agent running a loop. The graph coordinates the fleet. The loop makes each one trustworthy.**（グラフの各ノードは、ループを実行するエージェント。グラフが艦隊を調整し、ループが各ノードを信頼できるものにする）

> Everyone online builds the graph or the loop. **Nobody wires both.**（ネット上の誰もがグラフかループのどちらかを作る。**両方を配線する者はいない**）

著者は10個の実リポジトリを **ソースコードレベル**（READMEでなく実際のスケジューラ・フックスクリプト・ループ関数）で読んで両層を繋いだ。

> Claude Code は、あなたが1文字打つ前に、**見えも編集もできないビルトインツール説明で約16,000トークン**を消費する。Step L3 がそれを取り戻すリポジトリ。

## The Map（10リポジトリ・2層）

### THE GRAPH — 艦隊を調整する

| Step | リポジトリ | 星 | ライセンス | 役割 |
|---|---|---|---|---|
| **G1** | sipyourdrink-ltd/**bernstein** | 726 | Apache-2.0 | Orchestrate（オーケストレート） |
| **G2** | nekocode/**agent-worktree** | 267 | MIT | Fan out in isolation（隔離ファンアウト） |
| **G3** | wshobson/**agents** | 38,185 | MIT | Give each node a role（役割付与） |
| **G4** | fivetaku/**insane-research** | 108 | MIT | A graph that already ships（出荷済みグラフ） |

### THE LOOP — 1ノードを信頼できるものにする

| Step | リポジトリ | 星 | ライセンス | 役割 |
|---|---|---|---|---|
| **L1** | gastownhall/**beads** | 25,603 | MIT | Memory（メモリ） |
| **L2** | ShenSeanChen/**waku-agent** | 440 | MIT | The loop core（ループ本体） |
| **L3** | oraios/**serena** | 26,813 | MIT | Context（コンテキスト） |
| **L4** | obra/**superpowers** | 260,116 | MIT | Skills（スキル） |
| **L5** | hamelsmu/**claude-review-loop** | 706 | no license | The gate（ゲート） |
| **L6** | raindrop-ai/**workshop** | 937 | MIT | Proof（証明） |

> Every node in the graph is an agent running a loop. The graph decides **who runs and when**. The loop decides **whether you can trust what comes back**. Build the graph out of loops you can trust — or you've just built a faster way to ship bugs across a fleet.

---

## THE GRAPH 詳解

### G1 · Orchestrate → bernstein
5つのタスクを正しい順序で、一部並列に実行したい。それがグラフで、スケジュールする何かが必要。
- `core/orchestration/task_dag.py` がタスクDAGを読み込み、サイクルを検出し、1ウェーブずつ歩む
- `[P]` マークのタスクは並行 frozenset にバッチ化、残りは逐次
- 各 ready ノードは独自 git worktree で実際の `claude` CLI へ行き、lint/type/test ゲートの後、緑の時のみマージ
- **罠**: 重いプラットフォーム（1,765 Pythonファイル、独自 .sdd/ ワークスペース、ベアラートークンタスクサーバー）。DAG は手書き/コンパイル式で、グラフを推論してはくれない

### G2 · Fan out in isolation → agent-worktree
1リポジトリで5エージェントを並列に走らせるとファイルを踏み合う。解決策は**エージェントごとの git worktree**。
- `create_worktree` が `git worktree add -b <branch> <path> <base>` を実行
- マージは**最初に dry-run**、クリーンに適用できなければ "Merge aborted due to conflicts" で中止し `reset` で巻き戻し —— **リポジトリが半マージ状態に残らない**
- **罠**: worktree ごとのプリミティブであってスケジューラではない。何体 spawn するかは決めない（G1の仕事）

### G3 · Give each node a role → wshobson/agents
同一エージェントのグラフは只是個の遅いエージェント。各ノードは専門家であるべき。
- 94 プラグインにまたがる **203 の専門サブエージェント**（backend-architect, code-reviewer 等）
- 各々が YAML frontmatter + 実ロール本体を持つ Markdown ファイル
- ティアリング済み: アーキテクチャ/セキュリティ/レビュー55役は **Opus**、高速 ops は **Haiku**
- **罠**: 実際に使うプラグインだけインストール。203 全体をコンテキストに入れるのは、L3 で守った窓を燃やす方法

### G4 · A graph that already ships → insane-research
パターンを信じる必要はない。1つのプラグインが今日全体を走らせる。
- リアルな **7フェーズ研究グラフ**を実行する Claude Code プラグイン
- Phase 3 が Task ツールで 3-5 サブエージェントをファンアウト、**2-3 並行にスロットル**（>16エージェントのファンアウトが "Server is temporarily overloaded" を引き起こすのに対する硬いガード）
- **Phase 6 が盗む価値のある部分**: 決定的なコードゲート `scripts/validate_ledger.py` が各クレームを自身でスコアリングし、`verified_claims.json` に書ける唯一の存在。**ファンアウトした後、モデルでなくコードが何が生き残るかを決める**
- **罠**: validate ステップを飛ばすと synthesis が引用許可リストに何も持たない。ゲートはオプションの装飾でなく、ファンアウトが信頼できる唯一の理由

---

## THE LOOP 詳解

### L1 · Memory → beads
ビルドは todo リストでなく依存グラフ。エージェントはコンテキストリセットのたびに忘れる。
- markdown TODO を バージョン付き SQL 上のリアルグラフに置換
- `bd ready` はオープンなブロッカーのないタスクのみ返す
- `bd remember "insight"` はセッション・アカウントローテーションを生き残る
- `bd prime` が次セッションに**約50トークン**で再注入
- 古い完了作業は **70%小さい stub** に decay（エージェント自身が要約を書くので追加 API 請求なし）
- **罠**: disk I/O error (522) が出たら .beads/ が iCloud/Dropbox にある。クラウド同期が DB を破損させる。移動させよ

### L2 · The loop core → waku-agent
「ループ」を語る人は多いが、読めるほど小さいものを見せる人はほとんどいない。
- **推薦する中で意図的な最小リポジトリ**。全体のエージェントループは1ファイル `waku/loop/agent.py`（約95行）
- docstring の1行目が6つの正直な言葉で終わる: "This file is the whole trick."
- **done フラグなし** —— モデルがツール要求を止めた時に終わる。無限スピンを止める唯一のものは `for iteration in range(1, max_iterations + 1)`（デフォルト **10**）
- **罠**: ツールエラーを文字列に飲み込み、モデルが正常出力として読む。黙って誤ったツールが正しく見える。それはバグでなく **L5 が存在する理由**

### L3 · Context → serena
16,000トークンがどこへ行き、どう取り戻すか。
- serena の保守者達が Claude Code のビルトインツール説明を **~16k 編集不能トークン**と測定（Opus をファイル全体読みに偏らせる）
- `--context claude-code` で始めると、発見のために Read/Edit を禁止し、MCP 上で**シンボルレベル取得を強制**
- 2,000行のファイルでなく、1つの関数とその参照を引っ張る
- **罠**: serena は何も検証しない —— モデルに良すぎるツールを渡すだけ。最も強いリファクタ（シンボル/ファイル移動）には有料 JetBrains バックエンドが必要

### L4 · Skills → superpowers
スキルはノードが持つ手。ただし**セットアップで最も過大評価されている層**でもある。
- パックでなく完全なメソッド。TDDスキルが **"Iron Law"** を強制: "NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST"
- コードがテストより先に存在するなら "Delete means delete"
- anthropics/skills 形式上で構築。休眠スキルは説明がタスクに一致するまで **~100語**を costs
- **罠**: 説得であって syscall ではない。モデルは `assert True` を書き、赤を見たと主張できる。**本当の強制は L5**

### L5 · The gate → claude-review-loop
自分の宿題を採点するエージェントは自分のバグを出荷する。ループ内に第2モデルが必要。
- Claude Code の終了インターセプタである **Stop hook**
- エージェントが停止しようとすると `{"decision":"block"}` を返し、先に OpenAI Codex を走らせる: 最大4レビュアー並列（diff + OWASP / アーキテクチャ / Next.js / ブラウザ）
- `reviews/review-<id>.md` がディスクに存在するまで終了できない
- **設計で fail open**: ソースコメントは "never trap the user in a broken loop"
- **罠**: リポジトリに LICENSE ファイルなし、レビューファイルの存在だけチェック（エージェントは反論して所見をスキップできる）

### L6 · Proof → workshop
ループが良くなったと感じられる。**eval はそれを知る方法** —— そして最良の eval はそれ自身がループ。
- 実行をキャプチャし、その正確なトレースを編集後のコードに対してローカル daemon（ポート **5899**）で再駆動し、ツール呼び出しを diff
- エージェントのアサーションは ローカル trace DB 上の読み取り専用 SQL
- 失敗を見て、コードを直し、spans が緑になるまで再実行
- **罠**: replay はクリーンなエントリポイントを抽出した時にのみ安全。本番リクエストハンドラを指すと、本物の DB・課金・メールを叩く

---

## Assemble It（全体の組み立て）

トップダウンで読むと全体がスナップする:

> bernstein が仕事を DAG にコンパイルし、タスクごとにノードを spawn。agent-worktree が各ノードに隔離されたツリーを与える。wshobson/agents が役割を与える。
>
> そして各ノードの内側でループが走る。beads が記憶し、waku が反復し、serena が触れるコードだけを与え、superpowers が手を与え、claude-review-loop が第2モデルが署名するまで終了をブロックし、workshop が証明する。
>
> insane-research が今晚走らせられる1プラグインで全体の形: ファンアウトし、コードで検証し、統合。

## 金言 —— 「done を取り戻せるか」

> **One test grades all ten: can your system take done back?**（1つのテストが全10を採点する: あなたのシステムは done を取り戻せるか？）

- bernstein はゲートに失敗したノードのマージを拒否
- beads はタスクを not-ready に戻す
- review hook は終了したセッションを未完了に戻す
- workshop は緑のトレースを fail させる

> **A system that can only promote is a burndown chart with extra steps.**（昇格しかできないシステムは、余分なステップ付きのバーンダウンチャートに過ぎない）

> **Draw the graph. But build it out of loops first.**（グラフを描け。だが、まずループでそれを構築せよ）

---

## 関連（ボルト内）

- Graph+Loop の二層 → [[08_Agent Harness vs Loop vs Graph Engineering]]・[[02_LOOP vs GRAPH vs HARNESS ENGINEERING]]
- Loop層の信頼性（ゲート・検証） → [[09_Loop Engineering Claude,GPT 実戦で効くもの]]・[[01_エージェントファクトリの作り方 ビルダーズガイド]]
- Graph層のファンアウト・隔離 → [[10_1チャットをエージェントチームへ Opus5 12ステップ]]（worktree）
- 「doneを取り戻せる」= 根拠ベース停止 → [[02_24時間自走する自律型AIエージェントの設計図]]
- Graph は必要な時だけ → [[10_Graph Engineering 最大の間違い Loop↔Graph判断]]・[[07_Graph Engineering Clearly Explained]]
