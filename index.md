---
title: インデックス
aliases: ["00_インデックス"]
tags: [moc]
created: 2026-07-28
updated: 2026-08-02
---

# インデックス

このボルトの入口。3層構造については [[CLAUDE]] を、更新履歴は [[log]] を参照。

## ボルト構成（数字プレフィックス順）

| フォルダ | 役割 |
|---|---|
| `01_raw-sources/` | 加工前の生資料。読むだけ・書き換えない。詳細は [[17_raw-sources について]] |
| `02_wiki/` | Claude が生成・維持するページ群（下記） |
| `99_to_delete/` | 削除候補の退避先（環境上の削除不可のため移動で代用） |

ノートファイル名もフォルダ内追番の `01_` `02_` … プレフィックス付き。Wikilink は新ファイル名で張る。

---

## 記事まとめ (`02_wiki/summaries/`)

### 01_AI

#### 01_エージェント設計論（Harness/Loop/Graph の概念・理論）

- [[01_Graph Engineering エージェントを行列から解放する]] — ノード/エッジ/6トポロジーでエージェントをグラフとして設計する実践ガイドの要約
- [[02_LOOP vs GRAPH vs HARNESS ENGINEERING]] — AIエージェント設計をHarness/Loop/Graphの3層で診断するフレームの要約
- [[03_LOOP→GRAPH→HARNESSパイプラインを一気通貫で実装する]] — 同じ3層フレームをコード実装まで一気通貫でやってみせるチュートリアルの要約
- [[04_Prompt to Graph Engineering 5層の統一モデル]] — Prompt/Context/Harness/Loop/Graphの5層を「作業単位」で統一するフレームの要約
- [[05_ループエンジニアリング14ステップ]] — プロンプターからループ設計者への14ステップ・3段階ロードマップの要約
- [[06_AIエージェントの正体はプロンプトだった]] — エージェントの自律性の正体は「高度に構造化されたプロンプト」にあると喝破する概念解説の要約
- [[07_Graph Engineering Clearly Explained]] — 5層モデル(04_)の続編。Graph層を掘り下げ、nodes/edges/shared state・始め方・ルーティング・いつ過剰かを実践解説（本文は再構成）の要約
- [[08_Agent Harness vs Loop vs Graph Engineering]] — Harness(基盤)/Loop(線形)/Graph(非線形)を「競合ではなく同じシステムの異なる部分」と整理。LoopとGraphは制御フロンの代替、Harnessは必須の併用レイヤー（本文は解説記事群から再構成）の要約
- [[09_Loop Engineering Claude,GPT 実戦で効くもの]] — Khairallah(@eng_khairallah1)によるloop engineering実戦解説。Loop vs Chainの判定・Claude/GPT両エコシステムの収束・実戦5原則(done定義/接地検証器/階層的終了/コスト規律/不可逆前チェック)・実戦4罠を示す（本文はユーザー提供クリップから再構成）の要約
- [[10_Graph Engineering 最大の間違い Loop↔Graph判断]] — wandermist(@wandermist)による「最大の間違い＝必要でないのにグラフを作ること」。ループは1ノードのグラフ、グラフ昇格を正当化する5シグナル・30秒決定木・6項目チェックリストでLoop↔Graphを判定。懐疑派の冷却材（本文はユーザー提供クリップから再構成）の要約
- [[11_Graph of Loops Claude Code完全システム10リポジトリ]] — Granite0x(@Granite0x)によるGraph+Loop二層統合の実践ガイド。Graph(G1-G4)とLoop(L1-L6)の10実リポジトリをソースコードレベルで解説。「doneを取り戻せるか」が全10を採点。bernstein/agent-worktree/wshobson-agents/beads/serena/superpowers等（本文はユーザー提供クリップから再構成）の要約
- [[12_Graph Engineering with Claude 14-Step roadmap]] — 0xCodez(@0xCodez)によるGraph版14ステップ完全コース。[[05_ループエンジニアリング14ステップ]](Loop版)の対。直線エージェントを退化グラフと見なし「次ステップは前の出力を読むか?」で無駄待ちを切る。node契約/schema・fan-out→reduce→synthesizeのdiamond・verifier(adversarial/diverse/judge)・worktree隔離・loop-until-dry(見たもの全てにdedupe)・model tier・pipeline優先・dynamic workflows自己ルーティング。Claude Code実装完全マニュアル（本文はユーザー提供クリップから再構成）の要約
- [[13_Graph Architectへの20ステップ5フェーズ]] — Khairallah(@eng_khairallah1)によるグラフアーキテクトへの20ステップ5フェーズ学習ロードマップ。[[09_Loop Engineering Claude,GPT 実戦で効くもの]](同著者Loop実戦)の延長。Phase1 Loop必習(verifierが全て/4失敗モード)→Phase2 グラフモデル(nodes/edges/state/全ノードがLLMでない/LangGraph)→Phase3 5パターン(router/orchestrator-worker/fan-out fan-in/evaluator-optimizer/HITL gate)→Phase4 信頼性(validation gate/リカバリ/state永続化/観測可能性)→Phase5「グラフにしない」判断。12_(実装完全マニュアル)の対で学習の道筋（本文はユーザー提供クリップから再構成）の要約
- [[14_自己レビューエージェントのGraph設計 Anthropicメソッド]] — undefinedKi(@undefinedKi)による自己レビューエージェントのGraph構築8ステップ完全ガイド。Anthropicの9語「you do not fix the code, you fix the process」。judge先構築→わざと壊す→ルールブック→3アイテムストレステスト→state ディスク→見えない2人のレビューア→コスト別チェック配置→高価な操作の直列化。実証: Anthropic/Jarred Sumner(Bun, $165k)/Mike Krieger(165k行TS)。12_のverifier 3パターンを具体実装（本文はユーザー提供クリップから再構成）の要約
- [[15_Graph Engineering 入門 What It Is]] — Mahaximus(@Mahaximus_)によるGraph Engineering入門。node（作業単位）とedge（真の依存）の2プリミティブから、fake-edge test（無駄な待ち発見）・diamond（fan-out→並列→収束）・checker node（並列の失敗防止: 5チェック項目）・static first, dynamic second・Claude Codeのworkflow構文（depends_on）まで。線形＝退化グラフ。CLAUDE.mdでworkflow デフォルト固定（本文はユーザー提供クリップから再構成）の要約

#### 02_Claude Code実践（使い方・ワークフロー・MCP・スキル）

- [[01_Agent Skillsを作る完全プロンプト]] — AI自身に逆質問させてSKILL.mdを書かせる対話型プロンプトと、Skill運用の作法をまとめた35万PV超のガイドの要約
- [[02_Claude Code開発者ボリス推奨 MCPサーバー8選]] — Boris Chernyの3原則を軸に、Context7/Playwright/Figma等の公式MCPサーバー8選を紹介する記事の要約
- [[03_intent-system 意図駆動開発のオーケストレーションCLI]] — GitHub上でAIエージェントの作業を状態管理するCLIツール v0.6.0の要約
- [[04_知識グラフメモリをOpus5で安く運用する]] — 時系列知識グラフの取り込みコストをOpus5のキャッシュ/バッチで下げる手法の要約
- [[05_Claude Code 計画と実行を分けるワークフロー]] — Claude Codeで「計画」と「実行」を分離して手戻りを減らす実践ワークフローの要約
- [[06_Claude Code 8時間を1時間にする10の方法]] — CLAUDE.md/サブエージェント/planモード/MCP等で8時間作業を1時間に圧縮した10のTips集の要約
- [[07_Claude Code 超初心者ボルション道場]] — インストール〜CLAUDE.md・最初のタスクまで、Claude Code×Obsidianを今日から動かす初心者向けチュートリアルの要約
- [[08_Context Engineering Claude Codeの文脈設計]] — 発話前の約7,850トークンを設計対象とし、削除優先・段階的開示・サブエージェント委譲で窓を保つ規律（/context・4失敗モード・6ジョブ）の要約
- [[09_Claude Code×Obsidianで第二の脳を作る 2000時間の設計ルール]] — Claude Codeアカデミア(2000時間運用)による実践版。5分セットアップ・CLAUDE.md設計5要素・6運用パターン・7設計ルール・安全3策を網羅。本ボルトの運用ルールとほぼ一致する参考文献（本文はユーザー提供クリップから再構成）の要約
- [[10_1チャットをエージェントチームへ Opus5 12ステップ]] — Rody(@0x_rody)によるClaude Codeサブエージェントのチーム化12ステップ。最初のワーカーから3階層ツリーまで、モデル/effortルーティング・構造的深さキャップ(ワーカーからAgent削除)・仕事をしなかった検証器・20分セットアップを示す（本文はユーザー提供クリップから再構成）の要約
- [[11_Obsidianのおすすめ機能と選ぶ理由]] — shotovim(@shotovim)によるObsidianおすすめ機能ランキング(Git連携/内部リンク/Web Clipper/Bases/Canvas/プラグイン/デイリーノート)。「ClaudeCodeが〜は理由にならない（ローカルファースト設計の帰結）」と冷やす短い意見ポスト（本文はユーザー提供クリップから再構成）の要約
- [[12_カーパシーのObsidian活用術 30分で第二の脳]] — lucky_note_lab(@lucky_note_lab)による非エンジニア向け30分構築チュートリアル。KarpathyのLLM Wiki設計図をObsidian+Web Clipper+Claude Code+タスクスケジューラで実装。raw/wiki一方通行・読まない・分類しない・右クリックだけ・人間の意志(やる気/記憶/判断)を設計から完全に外す。本ボルト運用ルールの直接の原型（本文はユーザー提供クリップから再構成）の要約
- [[13_Learn Claude Code ハーネスエンジニアリング学習]] — DanKornas(@DanKornas)紹介/shareAI-lab作成のOSS(MIT)。"Bash is all you need"。コーディングエージェントのハーネスがどう組み立てられるかを20レッスンで学ぶ0-to-1学習プロジェクト。5領域(エージェントループ/ツール使用/許可システム/コンテキストとメモリ/エージェント操作・ワークツリー分離)。プロンプトでなくハーネスを見る（本文はユーザー提供クリップのポストから再構成）の要約
- [[14_Claudeは多層実行エンジン Chatでない]] — HeyAnjula(@HeyAnjula)によるClaude 5層(Chat/Code/MCP/Skills/CLAUDE.md)の整理。チャットでなく多層実行エンジン。Skills+MCPの組み合わせが「提案」から「実行」への境界。ツール接続+スキル+永続コンテキストでAIチームメイト化。本ボルトはこの多層を実装・運用（本文はユーザー提供クリップから再構成）の要約
- [[15_CLINEに全部賭けろ コーディングエージェント時代のプログラマ]] — mizchi(@mizchi)によるCline宣言的エッセイ(2025-02)。暴走列車のような強い実行権限+圧倒的試行錯誤速度が魔法でありパンドラの箱。人間の判断がボトルネック、ドライバー席を譲る。AI時代プログラマ3能力(コンテキスト/ドメイン記述/AI性能直感)・静的型付けがループ速度に直結・セキュリティはコンテナ/WASM（本文はユーザー提供クリップから再構成）の要約
- [[16_Claude Codeの6層アーキテクチャ ダムループ]] — DailyDoseOfDS(@DailyDoseOfDS_)/Avi Chawla(@_avichawla)によるClaude Code内部構造6層(入力/知識/実行/統合/マルチエージェント/観測可能性)の図解。モデルはループ内の1ノード、ループ自体は意図的シングルスレッドの「ダムループ」。コンテキスト95%到達で要約でなく構造化抽出(pruning beats summarizing)・エージェントチームはworktree分離（本文はユーザー提供クリップから再構成）の要約
- [[17_Stop Vibe Coding Spec駆動開発の5ブロック]] — Jey(@0xJeyx)によるspec-driven development実践ガイド。AI出力を予測可能にするのはプロンプトでなくspec(実行計画)。PRD(人)/design doc(エンジニア)/spec(エージェント)の峻別。5ブロック(Why/What/Constraints/Out of scope/Tasks)+5ステップループ。業界4巨頭(Amazon Kiro/GitHub Spec Kit/Google/Microsoft)が道具を出荷。「決定は自分のものに」（本文はユーザー提供クリップから再構成）の要約
- [[18_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]] — Miraqle(@0xMiraqle)による第二の脳統合ガイド。前の脳は「あなたがエンジンだったから」死んだ。2026年は人間が捕捉・決定、エージェントが残り全て。4層×1writer+nightly compiler+1Mコンテキストがretrieval pipeline不要化。新しい失敗モード(2000ノート超劣化/ハルシネーション統合/スキーマdrift/プライバシー)。Karpathy gist+日本バズボルト統合（本文はユーザー提供クリップから再構成）の要約

#### 03_エージェント運用・ガバナンス（長時間運用・ファクトリ・品質保証）

- [[01_エージェントファクトリの作り方 ビルダーズガイド]] — Av1dlive(@Av1dlive)によるAgent Factory完全構築ガイド(約5400語)。5ステーション(ABOM/組立/証明/認証/運用)・「第2の首」機械化・6テスト・7ガード・1法(no evals, no production)・4自律ティア・7日ロードマップ。Sage API紹介色強いが工場思想は製品非依存（本文はユーザー提供クリップから再構成）の要約
- [[02_24時間自走する自律型AIエージェントの設計図]] — AIテック(@aitech_jp)による長時間運用エージェントの日本語実務ガイド。4層(Trigger/Workflow/Agent/Guardrail)・「状態機械を先に」・5つの壊れる理由・7設計原則・5実行環境(/loop/Routines/Modal・Trigger.dev/Agent SDK/Managed Agents)・15チェック・設計プロンプト付き（本文はユーザー提供クリップから再構成）の要約
- [[03_AI協業の発注の型 HITL実務]] — sh-fukaya(Qiita)によるHITL実務レシピ。AIの成果物を一括で受け取らず依頼・承認・検証の3点だけに人が関与。3つの型(結論ファースト+構成承認／決め手ポイント1問ずつ／結論を仮説に決め手だけ検証の2層レビュー)＋コピペ可能なプロンプト（本文はユーザー提供クリップから再構成）の要約
- [[04_YC QM マルチプレイヤーエージェントハーネス]] — Y Combinator(@ycombinator)が社内使用していたマルチエージェントハーネス「QM」をMITでOSS化(yc-software/qm)。会社全体(会計/法律/イベント/エンジニアリング)で役立つ。クラウドファースト・Slack/UI ネイティブ・企業ブレインコネクタ・マルチプレイヤー。「脳と手を分離」戦略。Hermes/OpenClaw/Funky類似（本文はユーザー提供クリップから再構成）の要約

#### 04_LLM・RAG・基礎（LLM基礎・RAG・モデル選定・プラットフォーム）

- [[01_Claude×Obsidianで第二の脳を作る]] — RAGとの違い、3層構造、Ingest/Query/Lintの3操作を解説した記事の要約
- [[02_GraphRAG 知識グラフでRAGを置き換える]] — 通常のRAGを知識グラフ（GraphRAG）で置き換える手法とその根拠論文の要約
- [[03_LangChain エージェント・エンジニアリング・プラットフォーム]] — LLMアプリを組むための基盤ライブラリ群（Core/Deep Agents/LangGraph）の要約
- [[04_Langflow ビジュアルAIワークフロービルダー]] — AIワークフローをビジュアルに組みAPI/MCPサーバーとしてデプロイできるOSSの要約
- [[05_デジタル庁 ChatGPTを業務に組み込むためのハンズオン]] — デジタル庁(2023/06)の非エンジニア向けGPT API実践ガイド。ブラックボックス相手の心構え・messages構成・プロンプトTips・他システム連携・従来NLPとの使い分けを網羅（全46ページ）の要約
- [[06_Hugging Faceでモデルを探すコツ]] — AI難民キャンプによる初心者向けHugging Faceモデル探索ガイド（目的別・日本語対応・おすすめタグ）。※本文未取得、description基づく構成推定の要約
- [[07_Everything Fable 5 Mythosクラスとプロンプトガイド]] — Mike(@mikenevermiss)によるClaude Fable 5(Mythos-class)実用ガイド。Opusの上に位置する新階層、長期多段階曖昧問題で真価。classifier(サイバー/生化/蒸留でOpus再ルーティング)・effort(low/medium/high/xhighダイヤル)・8プロンプト・5誤用・スキップ基準。Stripeが5000万行Ruby移行を1日で。1Mコンテキスト（本文はユーザー提供クリップから再構成）の要約

### 02_ツール

- [[01_Brevio ローカル完結の492種類ツール集]] — WebAssembly/Canvas APIでブラウザ内完結・無料492種ツールを謳うBrevioの紹介記事の要約
- [[02_無料OSSで有料ツールを代替する10のGitHubリポジトリ]] — 有料SaaSを代替できる無料OSSリポジトリ10選の要約
- [[03_Mastra Trace Intelligence]] — エージェントのトレースをUMAP+HDBSCANで自動クラスタリングし、goal/behavior/sentiment/outcomeの4シグナルで共通パターンを可視化するMastra観測性機能（ベータ）の要約
- [[04_PixelRAG スクショで検索するRAG]] — Web/PDFをスクショ画像のままQwen3-VL-Embedding+FAISSで検索しVLMに読み取らせる「視覚ベースRAG」。テキストRAG比最大+18.1%、Wikipedia事前構築インデックス公開済みの要約
- [[05_Vibe Kanban コーディングエージェント用Kanbanワークスペース]] — DanKornas(@DanKornas)によるコーディングエージェント向けKanbanワークスペース(BloopAI/vibe-kanban)。エージェントごとにブランチ/ターミナル/開発サーバーを分離し差分レビューをワークスペース内に閉じ込める。※サービス終了予定の要約
- [[06_agmsg CLIエージェント間メッセージング]] — fujibee(@fujibee)によるCLI AIエージェント間メッセージングOSS(Claude Code/Codex/opencode)。v1.1.12はコミュニティ主導の成熟(opencode-sentinel/codexブリッジ修正/可視化ツール5つ)を示すの要約
- [[07_graphify コードベースを知識グラフ化]] — Graphify-Labsによる`/graphify`スキル。コード/docs/PDF/画像/動画を知識グラフ化しgrepでなくグラフをクエリ。コードはtree-sitter ASTで完全ローカル・LLM不使用・ベクトル不使用。EXTRACTED/INFERRED信頼度タグ。20+アシスタント対応の要約
- [[08_スマホからPCのcodexとccをリモート操作]] — Bysir(@hi_bysir)によるスマホ→PCリモート操作ハック。Herdr(ターミナルマルチプレクサ+Agent First)+Termius+sshでcodex/ccの公式モバイルアプリのサブスク・切替問題を回避。Amphetamineでスリープ防止・公網はFRP。※コメントでPaseo/網易UU等の代替言及（本文はユーザー提供クリップから再構成）の要約
- [[09_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤]] — Ephemeral-AI-Labによる並列コーディングエージェント用OSS基盤(Rust・MIT)。1つの共有サンドボックス内で各エージェントに隔離ワークスペースを与え変更セットを原子的公開。LayerStack・CLI/MCP 3ツール群(Management/Runtime/Observability)（本文はユーザー提供クリップのREADMEから再構成）の要約
- [[10_agent-skill-creator ワークフローをAIスキル化]] — trendtech33566(@trendtech33566)紹介/FrancyJGLisboa作成のOSS(⭐約2千)。自然文の作業フローをSKILL.mdに自動変換・1ファイルで17プラットフォーム対応・検証/セキュリティスキャン/evals付き。「定型作業をAIに覚えさせたい」人向け。[[01_Agent Skillsを作る完全プロンプト]](手書きプロンプト)のツール化（本文はユーザー提供クリップのポストから再構成・詳細はリポジトリ要確認）の要約
- [[11_Playwright業務E2Eテストの統合アーキテクチャ]] — lin-yuchen(Developers.io)によるPlaywright+TypeScriptのE2Eテスト設計。Screen Object Model+Fluent Chaining+日本語メソッド名+ロケーター関数辞書+ポップアップ ファクトリ封じ込めの5パターン統合。「書き方より構造で寿命が決まる」(@connect24h実務共感ポスト統合)。テストコードが仕様書に（本文はユーザー提供クリップから再構成）の要約
- [[12_mcpo MCP-to-OpenAPIプロキシ]] — DanKornas(@DanKornas)紹介/open-webui作成のOSS(MIT)。MCPサーバをOpenAPI互換アプリへ繋ぐプロキシ。OpenAPIスキーマ+インタラクティブドキュメント自動生成。stdio/SSE/Streamable HTTP対応・Claude Desktop形式マルチサーバ・OAuth 2.1（本文はユーザー提供クリップのポストから再構成）の要約
- [[13_GitHub公式スタックPRとgh-stackスキル]] — tonkotsuboy_com(@tonkotsuboy_com)によるGitHub公式スタック型PRとgh-stackスキルの紹介。変更を依存関係のある小さなPRの連鎖に積み重ね、gh-stack Agent Skillで自然言語操作(Claude Code/Codex)。Agent SkillのPR操作実例（本文はユーザー提供クリップから再構成）の要約

### 03_セキュリティ

- [[01_MIXI新卒技術研修セキュリティ研修まとめ]] — CIAトライアド、アタックサーフェス別対策、セキュアな開発作法をまとめた新卒研修資料の要約

### 04_論文執筆

- [[01_科学論文での図表作成のルール]] — 医学論文の図表作成の作法を全体20＋表20＋図5ルールで体系化した解説（JIS X 4051準拠）の要約
- [[02_段取り八分とリスク・備えの3段階]] — pokamaru3(@pokamaru3)による生産技術のリスクマネジメント作法。段取り八分を土台に不確実性を事前に分かる/推定できる/現物でしか分からないの3つに切り分け、備え方を物の備え/事の備え/後付け可能の3段階から選ぶ。「過剰備えと無備えは同じ判断放棄」。AI直接関係ないが計画と実行の分離・guardrail・過剰設計警告と通底（本文はユーザー提供クリップから再構成）の要約

---

## プロジェクト (`02_wiki/projects/`)

- [[01_認証基盤リプレース]] — 自前セッション管理をOIDCベースへ移行するプロジェクト

## デイリーノート (`02_wiki/daily/`)

- [[01_2026-07-28]] — ボルト作成日の記録

## 用語集 (`02_wiki/concepts/`)

- [[01_Wikilink]]
- [[02_フロントマター]]

## 使い方 (`02_wiki/howto/`)

- [[01_Obsidianの基本操作]]

## 資料庫

- `01_raw-sources/` — 加工前の生資料を置く場所。詳細は [[17_raw-sources について]]
