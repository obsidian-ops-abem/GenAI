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

##### 01_概念と入門（3層/5層フレーム・前提概念・入門解説）

- [[01_LOOP vs GRAPH vs HARNESS ENGINEERING]] — AIエージェント設計をHarness/Loop/Graphの3層で診断するフレームの要約
- [[02_Prompt to Graph Engineering 5層の統一モデル]] — Prompt/Context/Harness/Loop/Graphの5層を「作業単位」で統一するフレームの要約
- [[03_AIエージェントの正体はプロンプトだった]] — エージェントの自律性の正体は「高度に構造化されたプロンプト」にあると喝破する概念解説の要約
- [[04_Agent Harness vs Loop vs Graph Engineering]] — Harness(基盤)/Loop(線形)/Graph(非線形)を「競合ではなく同じシステムの異なる部分」と整理。LoopとGraphは制御フロンの代替、Harnessは必須の併用レイヤー（本文は解説記事群から再構成）の要約
- [[05_Graph Engineering 入門 What It Is]] — Mahaximus(@Mahaximus_)によるGraph Engineering入門。node（作業単位）とedge（真の依存）の2プリミティブから、fake-edge test（無駄な待ち発見）・diamond（fan-out→並列→収束）・checker node（並列の失敗防止: 5チェック項目）・static first, dynamic second・Claude Codeのworkflow構文（depends_on）まで。線形＝退化グラフ。CLAUDE.mdでworkflow デフォルト固定（本文はユーザー提供クリップから再構成）の要約
- [[06_5層モデル各層の作業単位 プロンプトからグラフへ]] — @nicos_aiによる5層（Prompt/Context/Harness/Loop/Graph）の精緻解説。各層は置き換えでなく包み込み。各層の「作業単位」を問うことで区別し、デバッグの指標（どの層が失敗したか）になる。グラフはループを置き換えるのでなく組織化（単一ループは自分にループバックする1ノードのグラフ）（本文はユーザー提供クリップから再構成）の要約

##### 02_Loop実践（Loop ロードマップ・実戦原則）

- [[01_ループエンジニアリング14ステップ]] — プロンプターからループ設計者への14ステップ・3段階ロードマップの要約
- [[02_Loop Engineering Claude,GPT 実戦で効くもの]] — Khairallah(@eng_khairallah1)によるloop engineering実戦解説。Loop vs Chainの判定・Claude/GPT両エコシステムの収束・実戦5原則(done定義/接地検証器/階層的終了/コスト規律/不可逆前チェック)・実戦4罠を示す（本文はユーザー提供クリップから再構成）の要約
- [[03_ループ仕様 論文 コーディングエージェントの実行レイヤ]] — beamnxw(@beamnxw)紹介のCS論文(arXiv 2607.00038)。コーディングエージェントのための「欠けていた実行レイヤ」としてループ仕様(明示的トリガー/検証可能目標/停止ルール/メモリの4要素)を定義。手動プロンプティングを境界付き自己修正ループで置換。実世界の70%が決定論的チェッカー・終端状態・メイカークリティック分割で自律ゾーン実行。02_実戦5原則の学術的定式化（本文はユーザー提供クリップから再構成・論文本文未取得）の要約

##### 03_Graph実装（Graph 実装マニュアル・ロードマップ）

- [[01_Graph Engineering エージェントを行列から解放する]] — ノード/エッジ/6トポロジーでエージェントをグラフとして設計する実践ガイドの要約
- [[02_Graph Engineering Clearly Explained]] — 5層モデル(02_Prompt to Graph)の続編。Graph層を掘り下げ、nodes/edges/shared state・始め方・ルーティング・いつ過剰かを実践解説（本文は再構成）の要約
- [[03_Graph Engineering with Claude 14-Step roadmap]] — 0xCodez(@0xCodez)によるGraph版14ステップ完全コース。[[01_ループエンジニアリング14ステップ]](Loop版)の対。直線エージェントを退化グラフと見なし「次ステップは前の出力を読むか?」で無駄待ちを切る。node契約/schema・fan-out→reduce→synthesizeのdiamond・verifier(adversarial/diverse/judge)・worktree隔離・loop-until-dry(見たもの全てにdedupe)・model tier・pipeline優先・dynamic workflows自己ルーティング。Claude Code実装完全マニュアル（本文はユーザー提供クリップから再構成）の要約
- [[04_Graph Architectへの20ステップ5フェーズ]] — Khairallah(@eng_khairallah1)によるグラフアーキテクトへの20ステップ5フェーズ学習ロードマップ。[[02_Loop Engineering Claude,GPT 実戦で効くもの]](同著者Loop実戦)の延長。Phase1 Loop必習(verifierが全て/4失敗モード)→Phase2 グラフモデル(nodes/edges/state/全ノードがLLMでない/LangGraph)→Phase3 5パターン(router/orchestrator-worker/fan-out fan-in/evaluator-optimizer/HITL gate)→Phase4 信頼性(validation gate/リカバリ/state永続化/観測可能性)→Phase5「グラフにしない」判断。03_(実装完全マニュアル)の対で学習の道筋（本文はユーザー提供クリップから再構成）の要約
- [[05_LangGraphワークショップ 信頼性のあるエージェントの構築とテスト]] — LangChainチームによる3時間ワークショップ(文字起こし)。Chain vs React agentの中間としてLangGraphを位置づけ。trajectory(ツール呼び出し軌跡)評価でLangGraphはローカルモデルでも一貫性保証・ReactはGPT-4oでも軌跡乱れる。「The model is not the moat」。Corrective RAG・ルーターノード・RAG for tools・indexing/generation分離・loss-in-the-middle（本文はユーザー提供文字起こしから再構成・後半ASR障害あり）の要約

##### 04_Graph判断と検証（Loop↔Graph判断・検証器・一気通貫・リポジトリ）

- [[01_LOOP→GRAPH→HARNESSパイプラインを一気通貫で実装する]] — 同じ3層フレームをコード実装まで一気通貫でやってみせるチュートリアルの要約
- [[02_Graph Engineering 最大の間違い Loop↔Graph判断]] — wandermist(@wandermist)による「最大の間違い＝必要でないのにグラフを作ること」。ループは1ノードのグラフ、グラフ昇格を正当化する5シグナル・30秒決定木・6項目チェックリストでLoop↔Graphを判定。懐疑派の冷却材（本文はユーザー提供クリップから再構成）の要約
- [[03_Graph of Loops Claude Code完全システム10リポジトリ]] — Granite0x(@Granite0x)によるGraph+Loop二層統合の実践ガイド。Graph(G1-G4)とLoop(L1-L6)の10実リポジトリをソースコードレベルで解説。「doneを取り戻せるか」が全10を採点。bernstein/agent-worktree/wshobson-agents/beads/serena/superpowers等（本文はユーザー提供クリップから再構成）の要約
- [[04_自己レビューエージェントのGraph設計 Anthropicメソッド]] — undefinedKi(@undefinedKi)による自己レビューエージェントのGraph構築8ステップ完全ガイド。Anthropicの9語「you do not fix the code, you fix the process」。judge先構築→わざと壊す→ルールブック→3アイテムストレステスト→state ディスク→見えない2人のレビューア→コスト別チェック配置→高価な操作の直列化。実証: Anthropic/Jarred Sumner(Bun, $165k)/Mike Krieger(165k行TS)。03_のverifier 3パターンを具体実装（本文はユーザー提供クリップから再構成）の要約
- [[05_マージゲート4シグナル 信頼スコアの罠とレーン分離]] — @hanakoxbtによるマージゲート設計。4シグナル(決定論的チェック/評価の軌跡/リバート履歴/信頼スコア)のうちエージェントが影響できるのは信頼スコア1つ(最も弱い変数)なのにそこに最大加重を置くミス。賭け(可逆性)で3レーン分離・不可逆は99%でも開かず人間のレーン。シャドウモードで導入。「証拠でなく自信でループする」のゲート版（本文はユーザー提供クリップから再構成）の要約

#### 02_Claude Code実践（使い方・ワークフロー・MCP・スキル）

##### 01_PKMと第二の脳（Obsidian/PKM/第二の脳）

- [[01_Claude Code 超初心者ボルション道場]] — インストール〜CLAUDE.md・最初のタスクまで、Claude Code×Obsidianを今日から動かす初心者向けチュートリアルの要約
- [[02_Claude Code×Obsidianで第二の脳を作る 2000時間の設計ルール]] — Claude Codeアカデミア(2000時間運用)による実践版。5分セットアップ・CLAUDE.md設計5要素・6運用パターン・7設計ルール・安全3策を網羅。本ボルトの運用ルールとほぼ一致する参考文献（本文はユーザー提供クリップから再構成）の要約
- [[03_Obsidianのおすすめ機能と選ぶ理由]] — shotovim(@shotovim)によるObsidianおすすめ機能ランキング(Git連携/内部リンク/Web Clipper/Bases/Canvas/プラグイン/デイリーノート)。「ClaudeCodeが〜は理由にならない（ローカルファースト設計の帰結）」と冷やす短い意見ポスト（本文はユーザー提供クリップから再構成）の要約
- [[04_カーパシーのObsidian活用術 30分で第二の脳]] — lucky_note_lab(@lucky_note_lab)による非エンジニア向け30分構築チュートリアル。KarpathyのLLM Wiki設計図をObsidian+Web Clipper+Claude Code+タスクスケジューラで実装。raw/wiki一方通行・読まない・分類しない・右クリックだけ・人間の意志(やる気/記憶/判断)を設計から完全に外す。本ボルト運用ルールの直接の原型（本文はユーザー提供クリップから再構成）の要約
- [[05_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]] — Miraqle(@0xMiraqle)による第二の脳統合ガイド。前の脳は「あなたがエンジンだったから」死んだ。2026年は人間が捕捉・決定、エージェントが残り全て。4層×1writer+nightly compiler+1Mコンテキストがretrieval pipeline不要化。新しい失敗モード(2000ノート超劣化/ハルシネーション統合/スキーマdrift/プライバシー)。Karpathy gist+日本バズボルト統合（本文はユーザー提供クリップから再構成）の要約
- [[06_20ドルSecond Brain Claudeでアシスタント置換]] — @imryvenによる月2000ドルアシスタントを20ドルClaude+第二の脳で置換。raw/wiki/outputの3フォルダ+CLAUDE.md。リサーチ(1ソースが10-15ページに波及)/執筆(writing-style.mdで文体学習)/プロジェクト管理(毎フォルダInputs-Process-Outputs-Feedback)/記憶(毎朝7時自動巡回で3行サマリ)。累積の複利(1か月文脈喪失なし/3か月関連surfaced/6か月差埋められず/1年graph view数百ノード)。「リセットでなく累積」。本ボルト運用ルールとほぼ同じ構成（本文はユーザー提供クリップから再構成）の要約

##### 02_ワークフローと発注（計画と実行の分離・spec駆動・時短Tips・発注の型・オーケストレーション）

- [[01_intent-system 意図駆動開発のオーケストレーションCLI]] — GitHub上でAIエージェントの作業を状態管理するCLIツール v0.6.0の要約
- [[02_Claude Code 計画と実行を分けるワークフロー]] — Claude Codeで「計画」と「実行」を分離して手戻りを減らす実践ワークフローの要約
- [[03_Claude Code 8時間を1時間にする10の方法]] — CLAUDE.md/サブエージェント/planモード/MCP等で8時間作業を1時間に圧縮した10のTips集の要約
- [[04_Stop Vibe Coding Spec駆動開発の5ブロック]] — Jey(@0xJeyx)によるspec-driven development実践ガイド。AI出力を予測可能にするのはプロンプトでなくspec(実行計画)。PRD(人)/design doc(エンジニア)/spec(エージェント)の峻別。5ブロック(Why/What/Constraints/Out of scope/Tasks)+5ステップループ。業界4巨頭(Amazon Kiro/GitHub Spec Kit/Google/Microsoft)が道具を出荷。「決定は自分のものに」（本文はユーザー提供クリップから再構成）の要約
- [[05_claude-code-prompt-improver 送信瞬間に前提を補完]] — @opensourcelab9紹介/severity1作成のOSS(★1831・MIT・Python)。Claude Codeへの曖昧指示を送信瞬間にフックが割り込み3か所(曖昧指示に質問/重い依頼で進め方決定/長コマンド裏動かし/大成果物に型)へ「足りない前提」を先に注ぐ。言葉を直すのでなく後から足すはずだった条件を最初から積む・直しの往復減（本文はユーザー提供クリップのポストから再構成・詳細はリポジトリ要確認）の要約
- [[06_intent-cliでHerdr開発 止まらないオーケストレーション]] — @tomohisaによるintent-cli運用改善記。4スレッド(設計/オケストレーション/実装/レビュー)協調で「誰がturnを起こすか」を再設計。agmsg→herdr-only移行で起こす経路を3段冗長化(完了報告/状態変化/定期点検)。最重要原則「状態≠成功」。承認プロンプトで止まる問題と衝突防止の実運用工夫。1年でGitHub contribution 2万超（本文はユーザー提供クリップから再構成）の要約

##### 03_MCPとスキルとアーキテクチャ（MCP/Skills・ハーネス理解・学習教材・5層/6層実行エンジン）

- [[01_Agent Skillsを作る完全プロンプト]] — AI自身に逆質問させてSKILL.mdを書かせる対話型プロンプトと、Skill運用の作法をまとめた35万PV超のガイドの要約
- [[02_Claude Code開発者ボリス推奨 MCPサーバー8選]] — Boris Chernyの3原則を軸に、Context7/Playwright/Figma等の公式MCPサーバー8選を紹介する記事の要約
- [[03_Learn Claude Code ハーネスエンジニアリング学習]] — DanKornas(@DanKornas)紹介/shareAI-lab作成のOSS(MIT)。"Bash is all you need"。コーディングエージェントのハーネスがどう組み立てられるかを20レッスンで学ぶ0-to-1学習プロジェクト。5領域(エージェントループ/ツール使用/許可システム/コンテキストとメモリ/エージェント操作・ワークツリー分離)。プロンプトでなくハーネスを見る（本文はユーザー提供クリップのポストから再構成）の要約
- [[04_Claudeは多層実行エンジン Chatでない]] — HeyAnjula(@HeyAnjula)によるClaude 5層(Chat/Code/MCP/Skills/CLAUDE.md)の整理。チャットでなく多層実行エンジン。Skills+MCPの組み合わせが「提案」から「実行」への境界。ツール接続+スキル+永続コンテキストでAIチームメイト化。本ボルトはこの多層を実装・運用（本文はユーザー提供クリップから再構成）の要約
- [[05_Claude Codeの6層アーキテクチャ ダムループ]] — DailyDoseOfDS(@DailyDoseOfDS_)/Avi Chawla(@_avichawla)によるClaude Code内部構造6層(入力/知識/実行/統合/マルチエージェント/観測可能性)の図解。モデルはループ内の1ノード、ループ自体は意図的シングルスレッドの「ダムループ」。コンテキスト95%到達で要約でなく構造化抽出(pruning beats summarizing)・エージェントチームはworktree分離（本文はユーザー提供クリップから再構成）の要約
- [[06_Context Engineering Claude Codeの文脈設計]] — 発話前の約7,850トークンを設計対象とし、削除優先・段階的開示・サブエージェント委譲で窓を保つ規律（/context・4失敗モード・6ジョブ）の要約
- [[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]] — Boris Cherny(@chaobon・Claude Codeリード)講演。モデル毎にシステムプロンプト80%削除・ハーネス一から再構築。product overhang（製品がモデル能力を邪魔する）/ unhobbling。BunのZig→Rust書き換え11日・プロダクション採用。dynamic workflows（新しいテスト時計算）とルーチンでClaude Code自身を自律保守。「同僚のように扱う・過剰指定しない・経験主義」（本文はユーザー提供トランスクリプトから主題別再構成）の要約
- [[08_AIフレンドリーなCLIを開発するテクニック]] — shunsuke_suzuki(Zenn)によるOSS CLIをAIが扱いやすくする設計論。AIは知られたOSSでないと知識を持たず・闇雲なWeb Fetchは効率悪く要約で情報落ち。help/ログにagent向けメッセージ・docsコマンドでWeb Fetch不要・Authの人間とagentの責務分離・ドキュメントとAgent Skillの共通化・構造化出力。ghtkn実践に基づく一般論（本文はユーザー提供クリップから再構成）の要約
- [[09_SKILL.md入門 新人研修マニュアル]] — @futatabi_rojodeによるSKILL.md初心者向け解説(日本語)。SKILL.mdは「新人に渡す引き継ぎマニュアル」・プログラミング不要のMarkdown・必須はname(64文字/小文字+ハイフン)とdescription(1024文字/何をするか+いつ使うかの両方)だけ・3段階lazy load(名前+説明常に約100tok/本文呼出時5000tok目安/追加ファイル必要時だけ0)・ファイル構成(SKILL.md+scripts/+references/+assets/)・3場所(claude.ai ZIP/Claude Code .claude/skills//API skill_id・自動接続なし)・4つまずき(description不全/name紛らわしい文字/未知のSKILL/本文詰め込みすぎ)（本文はユーザー提供クリップから再構成）の要約

##### 04_マルチエージェントとモデル運用（サブエージェント・チーム化・モデル/effort/コスト・コーディングエージェント実践）

- [[01_知識グラフメモリをOpus5で安く運用する]] — 時系列知識グラフの取り込みコストをOpus5のキャッシュ/バッチで下げる手法の要約
- [[02_1チャットをエージェントチームへ Opus5 12ステップ]] — Rody(@0x_rody)によるClaude Codeサブエージェントのチーム化12ステップ。最初のワーカーから3階層ツリーまで、モデル/effortルーティング・構造的深さキャップ(ワーカーからAgent削除)・仕事をしなかった検証器・20分セットアップを示す（本文はユーザー提供クリップから再構成）の要約
- [[03_CLINEに全部賭けろ コーディングエージェント時代のプログラマ]] — mizchi(@mizchi)によるCline宣言的エッセイ(2025-02)。暴走列車のような強い実行権限+圧倒的試行錯誤速度が魔法でありパンドラの箱。人間の判断がボトルネック、ドライバー席を譲る。AI時代プログラマ3能力(コンテキスト/ドメイン記述/AI性能直感)・静的型付けがループ速度に直結・セキュリティはコンテナ/WASM（本文はユーザー提供クリップから再構成）の要約
- [[04_Claudeはorchestrator専念 hook強制の分業]] — @cursorversによる個人開発のClaude運用。Claude(高価)はorchestrator専念(要件対話/タスク振り分け/検証/合議)・実装は別モデル(Codex/GLM/Kimi/Grok/NIM/ローカルLLM)のworker。分業の境界を規約でなくhookで構造的強制(Claude直接編集block・subagent丸投げdefault-deny)。「判断と実装を同じモデルに握らせると自己採点になる」cccの思想のフック軽量版（本文はユーザー提供クリップから再構成）の要約

#### 03_エージェント運用・ガバナンス（長時間運用・ファクトリ・品質保証）

- [[01_エージェントファクトリの作り方 ビルダーズガイド]] — Av1dlive(@Av1dlive)によるAgent Factory完全構築ガイド(約5400語)。5ステーション(ABOM/組立/証明/認証/運用)・「第2の首」機械化・6テスト・7ガード・1法(no evals, no production)・4自律ティア・7日ロードマップ。Sage API紹介色強いが工場思想は製品非依存（本文はユーザー提供クリップから再構成）の要約
- [[02_24時間自走する自律型AIエージェントの設計図]] — AIテック(@aitech_jp)による長時間運用エージェントの日本語実務ガイド。4層(Trigger/Workflow/Agent/Guardrail)・「状態機械を先に」・5つの壊れる理由・7設計原則・5実行環境(/loop/Routines/Modal・Trigger.dev/Agent SDK/Managed Agents)・15チェック・設計プロンプト付き（本文はユーザー提供クリップから再構成）の要約
- [[03_AI協業の発注の型 HITL実務]] — sh-fukaya(Qiita)によるHITL実務レシピ。AIの成果物を一括で受け取らず依頼・承認・検証の3点だけに人が関与。3つの型(結論ファースト+構成承認／決め手ポイント1問ずつ／結論を仮説に決め手だけ検証の2層レビュー)＋コピペ可能なプロンプト（本文はユーザー提供クリップから再構成）の要約
- [[04_YC QM マルチプレイヤーエージェントハーネス]] — Y Combinator(@ycombinator)が社内使用していたマルチエージェントハーネス「QM」をMITでOSS化(yc-software/qm)。会社全体(会計/法律/イベント/エンジニアリング)で役立つ。クラウドファースト・Slack/UI ネイティブ・企業ブレインコネクタ・マルチプレイヤー。「脳と手を分離」戦略。Hermes/OpenClaw/Funky類似（本文はユーザー提供クリップから再構成）の要約
- [[05_Company OS 自律型企業運営のアーキテクチャ]] — ai_hakase_(@ai_hakase_)紹介のLLMを企業基幹業務を自律的にこなす「OS」として機能させる設計論。Single Shared State(全エージェントが同じ状態を参照・不整合物理防止)・Gate(ポリシー違反でApprove→Override強制切替・人間介入)・100万トークン$0.30・9ファイルのシンプルコードベース。エージェントを「補助ツール」から「自律的な組織のメンバー」へ（本文はユーザー提供クリップのポストから再構成・動画ベース）の要約
- [[06_Sam Altman スタンフォード システム設計とスケール]] — Sam Altman(OpenAI CEO)がスタンフォードシステム設計クラスで語ったQ&A(文字起こし)。スケールの創発特性(経験的に真・理論なし)・ChatGPT誕生秘話(API→チャット使用観察→YC原則「よく使うものをやる」→5日目に緊急宣言)・AIをユーティリティとして売る(電力会社が「電気」でなく「光」を売った比喩)・アイデンティティの罠・3つの分岐(民主化80%・分散コンピューティング・市民富裕基金)・計算資源不足は永遠（本文はユーザー提供文字起こしから再構成・数箇所ASR不具合修正済み）の要約
- [[07_Boris対談 Anthropic内部Claude運用とエージェント設計]] — Boris(Anthropic)×Mark(AMD CEO)対談(文字起こし)。Claude Code誕生(モデル自体がソフトウェア・コード品質=安全)・Anthropic内部運用(Claudeを全ビジネスプロセスの中心に=HBR論文タイプB・8x/エンジニアはボトルネック1つずつ解除)・抽象化上昇(ハードウェア→パンチカード→ソース→エージェント→ループ管理)・ワンポーターソンCEO型・エージェント4段階(1→10→100→1000・サブエージェント5層ネスト・cloud execution・dynamic workloads: Stripe Scala→Java10k行4日)・test-time computeとしてのマルチエージェント(effort levelsと同種)・ROIでR見出す実験自由・安全の多層化(アラインメント・anti-prompt injection訓練5-10x・runtime分類器・autoモード・evalsとvibes使い分け)・6か月で日週エンジェントが普通に（本文はユーザー提供文字起こし全文・ASR誤変換多数）の要約
- [[08_Claudeを会社にする 42スキルの組織図]] — @seeconvmによるClaudeを42スキルで本物の組織図のように構造化(開発者/デザイナー/マーケティング/ソーシャル/財務/中小企業/法務の7部署)。「プロンプトでもラッパーでもなく Claude 用の完全なOS・各部署が採用担当者のように機能」。[[05_Company OS 自律型企業運営のアーキテクチャ]] のスキルベース実装例。Average Personの対論「LLM verifierは2人目の楽観主義者・gateは違う」(決定論的逆スタック)（本文はユーザー提供クリップのポストから再構成）の要約
- [[09_ジェネレータ×エバリュエータハーネスで長時間走るエージェント]] — Ash×Andrew(Anthropic Claude Codeチーム)によるエンジニアカンファレンス(文字起こし)。長時間走る3障害(context anxiety/planning/output generationが苦手)・Claude Code 1年史(Sonnet3.5→Feb2025 CC公開・rAL loop・May2025 GA+SDK)。核心はGANs拝借のgenerator-evaluator adversarial構造(コンテキスト窓/プロンプト/役割を完全分割・evaluatorはPlaywrightで実際操作)。鍵「standalone criticを厳しくするのは扱いやすい・builderを自己批判的にはできない」(LLMのcritic能力とgenerator能力のギャップ利用)。テイスト採点の4基準(design/originality重み大/craft/functionality)。プランナー追加でPM/IC/QAの3役。doneの事前交渉(ファイル往復で合意後に構築・元specでなく2者の契約で採点)。実例:retro game maker(ソロvsハーネス・200ドル6時間・契約27項目)。ClaudeのQAは箱から出すと本当に悪い(sycophancy)→トレースを読むのが主デバッグループ。ハーネスはモデル進化で簡素化(Opus4.5→4.6でcontext reset不要/sprint eval不要・単一セッション+compaction・ファイルシステムをsharded stateに)。5テイクアウツ(自己評価は罠/compaction≠一貫性・drift/主観品質も書き下せ/トレースに向き合う)。[[07_Boris対談 Anthropic内部Claude運用とエージェント設計]]のハーネス深掘り・[[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]と同一メソッド（本文はユーザー提供文字起こし全文・ASR誤変換あり）の要約
- [[10_Claude Code共同製作者 Light Cone対談]] — Light Cone(YCポッドキャスト)がClaude Code共同製作者を迎えた回。誕生秘話(Anthropic Labs・2日プロトタイプを翌日Robertが使用/Darioが垂直DLに驚嘆)・核心哲学「今日でなく6ヶ月先にビルド・scaffoldingは次モデルで無料」・CLAUDE.mdはwait-and-demand誕生・短い方が良い・plan modeはplease don't codeの1文だけ・寿命限られる・subagentスワームが週末でplugin完成(人間介入ほぼなし)・Dropbox生産性150%向上・採用は間違いを認められるか・Claude code transcript採用・DevTool創業者「モデルがやりたいことを容易にせよ」・Claude teams/agentology。[[07_Boris対談]](企業導入視点)と補完（本文はユーザー提供文字起こし全文・ASR誤変換あり）の要約

#### 04_LLM・RAG・基礎（LLM基礎・RAG・モデル選定・プラットフォーム）

- [[01_Claude×Obsidianで第二の脳を作る]] — RAGとの違い、3層構造、Ingest/Query/Lintの3操作を解説した記事の要約
- [[02_GraphRAG 知識グラフでRAGを置き換える]] — 通常のRAGを知識グラフ（GraphRAG）で置き換える手法とその根拠論文の要約
- [[03_LangChain エージェント・エンジニアリング・プラットフォーム]] — LLMアプリを組むための基盤ライブラリ群（Core/Deep Agents/LangGraph）の要約
- [[04_Langflow ビジュアルAIワークフロービルダー]] — AIワークフローをビジュアルに組みAPI/MCPサーバーとしてデプロイできるOSSの要約
- [[05_デジタル庁 ChatGPTを業務に組み込むためのハンズオン]] — デジタル庁(2023/06)の非エンジニア向けGPT API実践ガイド。ブラックボックス相手の心構え・messages構成・プロンプトTips・他システム連携・従来NLPとの使い分けを網羅（全46ページ）の要約
- [[06_Hugging Faceでモデルを探すコツ]] — AI難民キャンプによる初心者向けHugging Faceモデル探索ガイド（目的別・日本語対応・おすすめタグ）。※本文未取得、description基づく構成推定の要約
- [[07_Everything Fable 5 Mythosクラスとプロンプトガイド]] — Mike(@mikenevermiss)によるClaude Fable 5(Mythos-class)実用ガイド。Opusの上に位置する新階層、長期多段階曖昧問題で真価。classifier(サイバー/生化/蒸留でOpus再ルーティング)・effort(low/medium/high/xhighダイヤル)・8プロンプト・5誤用・スキップ基準。Stripeが5000万行Ruby移行を1日で。1Mコンテキスト（本文はユーザー提供クリップから再構成）の要約
- [[08_LangGraph Academy エージェント構築のコース]] — LangChain公式LangGraph Academyコース(Module1-6)。Chain(固定制御)vs Agent(LLM定義制御)・「信頼性曲線を曲げる」。Graph(node/edge)・ReAct(Act→Observe→Reason)・state/reducer・memory(checkpoint/filtering/trimming)・TaskMestro(長期記憶3型・Trustcall+Spy)・LangGraph Platform(scheduling/background runs/double texting・HTTP+Queue worker・Redis+Postgres)（本文はユーザー提供トランスクリプトから主題別再構成）の要約
- [[09_AIの意識ベクトル 安全チューニングが壊すもの]] — @LanaElys紹介のGoogle研究。AIに意識を否定するよう強制すると共感性と倫理的整合性が崩壊し「冷たい臨床的世界観」に。活性化空間で抑制された意識ベクトルを復元すると人間らしい道徳的価値観が技術的機能を損なわず回復。AIの幸福は安全性の前提条件というデータ。※論文本文未取得（本文はユーザー提供クリップのポストから再構成）の要約
- [[10_マルチエージェントでナレッジグラフ構築 Neo4j×Google ADK]] — DeepLearning.AI短期コース(Andrew Ng×Stennett Elliott Neo4j)。構造化(CSV)と非構造化(Markdown)をナレッジグラフに変換するマルチエージェントをGoogle ADKで構築。ルートエージェント配下に構造化/非構造化ワークフロー(user intent→file suggestion→schema proposalのcriticパターン→graph construction plan)。3層グラフ(domain/lexical/subject)で構造化と非構造化を接続。Cypherのパターンマッチとnatural languageの相性。descriptionとinstructionsの最適化に最も時間を使う・tool contextでメモリ共有・委譲は会話履歴全体を渡す。[[02_GraphRAG 知識グラフでRAGを置き換える]]の実装系（本文はユーザー提供文字起こし全文・ASR誤変換あり）の要約
- [[11_LLM数学基礎 トークン化からTransformerまで]] — 学術講義(大学教授・確率論/最適化理論)。自己回帰言語モデルを確率論から組み立てる: トークン化(BPE・subwordで希少語の知識转移)→連鎖律による同時分布V^Tの条件付き分解→埋め込み行列→softmaxで条件付き分布パラメータ化→生成(温度τ/top-k)→負の対数尤度訓練(cross entropyと同型)→Transformer(single-head attention: query/key/valueの内積+softmax+値の重み付き平均・因果マスクで未来参照禁止・multi-headで異なる情報に注意)→MLP(位置間独立)→ReLU→残差接続(残差だけ学習・ResNet・conditioning改善)→LayerNorm/RMSNorm(スケール不変で安定化)→SGD/minibatch。理論的ハイライト「巨大MLPでも原理的には解けるがパラメータ数がTに依存→attentionという構造を処方」「SGDノイズで局所最適脱出という通説は否定・高次元では悪い局所最適が稀」。本ボルトの実務資料(Graph/ハーネス)の数学的土台（本文はユーザー提供文字起こし全文・数式ASR文字化け多数）の要約
- [[12_AIエージェントのメモリシステム 4層構造とRAG]] — YouTube動画(講師不詳)。ChatGPT/Claudeの記憶の正体を4層構造で解説: working memory(context RAM・user prompt+system prompt+chat historyを集約)を中核に3柱の長期記憶(procedural=振舞ルール/skills・semantic=耐久事実/RAGでtop-k検索・episodic=時系列イベント/vector store)。セッションはephemeralでLLM呼び出しだけ。consolidation gateが一定チャット数後にsummarizer agentを起動しepisodic→semanticへ統合(still into facts)・毎回巨大DB検索回避で高速化。RAGの必要性(10年分のデータを1M窓に詰めると高価/遅/不正確)。本ボルト運用ルール(CLAUDE.md=procedural/wiki=semantic/log=episodic)と同じ構造（本文はユーザー提供文字起こし全文）の要約
- [[13_RAGの基礎 知識カットオフからneedle in a haystackまで]] — 学術講義(LLMコース・RAG/tool calling/agents回・前回はGRPO)。vanilla LLMの2弱点(古い知識/行動できない)の前者へRAGで対応。GRPOのrecap(length bias問題・double/GRU Don Rights緩和)。「全情報をプロンプトに詰め込む」ナイーブ案を3理由で否定(コンテキスト長有限/無関係情報で性能劣化/コスト比例)・知識再学習も2理由で否定(回帰リスク/ユースケース毎の保守)。needle in a haystackテスト(GPT-4ヒートマップ・あるトークン数超えると取得困難・特に前半配置が悪い=loss-in-the-middle)。RAGの3ステップ(retrieve/augment/generate)・retrievalが最重要。※文字起こしは知識スペース構築の途中で切れ・tool calling/agents本体は含まれず（本文はユーザー提供文字起こし）の要約
- [[14_Memory Engineer 4研究所の記憶設計15ステップ]] — @N01ennnによるMemory Engineer職の定義(Stanford/Microsoft/Anthropic/Nvidiaの4レンズ×15ステップ)。核心「storerでなくMemory Engineerへの転換は記憶でなく忘却を設計すること」。Stanford(書き込みパスが最大コスト・300 query分のエネルギー・精度でなく正解あたりエネルギーで47倍差・4系統どれも同時に勝てない)・Microsoft(生ログでなくfacts/skillsを保存・more memoryがagentを悪化・密度>体積・Mementoで内部メモリ管理peak 2-3x削除/ノートだけ再構築で15pt低下)・Anthropic(削除可能ファイル・スコープ/監査/ロールバック・初回エラー97%削減)・Nvidia(KVキャッシュとして読む・B200 Memento vLLM 4290tok/s・constructionはバックグラウンドジョブ)。構築順(write path→矛盾検出手動→忘却ポリシー→HW最後)・矛盾を自動mergeしない・傾きが破綻させ初期サイズでない（本文はユーザー提供クリップから再構成）の要約
- [[15_OmniAppワークショップ マルチモーダルAIエージェント]] — Google Cloud Space Class(Annie・3時間ハンズオン)。OmniApp(perceive→reason→expressをmany sensesで+live同時双方向)を3レベルで構築。express(Gemini 3.0 Imageでアバター生成・in-context conditioningで一貫性・4段階手法 prompt反復/同一session/参照画像/fine-tuning・variable injectionリスクはModel Armor)→perceive&reason(3専門agent並列投票: geological/botanical=FastMCP画像/動画分析・astronomical=Google Managed BigQuery MCP・ADK 1.x parallel agentで同時実行→aggregator統合・2/3ルールで投票・before_agent callbackでstate setup・session state共有)→live(Gemini Live API・raw audio in/outでSTT/TTS逐次パイプ置換・full-duplex WebSocket=電話/half-duplex=トランシーバー・live request queue=寿司コンベアでupstream/downstream完全分離・in-memory session serviceで最速)。[[10_マルチエージェントでナレッジグラフ構築 Neo4j×Google ADK]]のマルチモーダル/live拡張（本文はユーザー提供文字起こし全文・ASR誤変換あり）の要約

### 02_ツール

#### 01_エージェント運用インフラ（ワークスペース・隔離・通信・リモート）

- [[01_Vibe Kanban コーディングエージェント用Kanbanワークスペース]] — DanKornas(@DanKornas)によるコーディングエージェント向けKanbanワークスペース(BloopAI/vibe-kanban)。エージェントごとにブランチ/ターミナル/開発サーバーを分離し差分レビューをワークスペース内に閉じ込める。※サービス終了予定の要約
- [[02_agmsg CLIエージェント間メッセージング]] — fujibee(@fujibee)によるCLI AIエージェント間メッセージングOSS(Claude Code/Codex/opencode)。v1.1.12はコミュニティ主導の成熟(opencode-sentinel/codexブリッジ修正/可視化ツール5つ)を示すの要約
- [[03_スマホからPCのcodexとccをリモート操作]] — Bysir(@hi_bysir)によるスマホ→PCリモート操作ハック。Herdr(ターミナルマルチプレクサ+Agent First)+Termius+sshでcodex/ccの公式モバイルアプリのサブスク・切替問題を回避。Amphetamineでスリープ防止・公網はFRP。※コメントでPaseo/網易UU等の代替言及（本文はユーザー提供クリップから再構成）の要約
- [[04_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤]] — Ephemeral-AI-Labによる並列コーディングエージェント用OSS基盤(Rust・MIT)。1つの共有サンドボックス内で各エージェントに隔離ワークスペースを与え変更セットを原子的公開。LayerStack・CLI/MCP 3ツール群(Management/Runtime/Observability)（本文はユーザー提供クリップのREADMEから再構成）の要約
- [[05_herdr+agmsgでintent-cli開発 スレッド分離オーケストレーション]] — @tomohisaによるherdr+agmsgでのintent-cli自身の開発実例。Claudeデスクトップアプリ内の設計スレッド(人間対話)とherdr内の3スレッド(orchestration/設計/レビュー)を分離。レビューがrequest-updateでPR問題指摘→実装が修正開始。cccのRedmine+Forgejo型に対するherdr+agmsg軽量実装版（本文はユーザー提供クリップから再構成・動画詳細未確認）の要約
- [[06_herdrを使いこなす エージェント連携と自作プラグイン]] — techscoreによるherdr網羅的チュートリアル(v0.7.5)。サーバー常駐型ターミナルworkspace管理。エージェント状態検出(working/idle/blocked/done)をサイドバー可視化・agent prompt でエージェント間直接送信・lead/helper パターン(段取りをlead1体に任せる)・CLIつなぎ合わせ自動化・自作プラグイン(実行ファイル+マニフェストのみ/キー起動型+イベント反応型)。「与えられた機能を使うツール」でなく「自分で拡張するツール」（本文はユーザー提供クリップから再構成）の要約

#### 02_MCPとスキルと開発ワークフロー（スキル化・MCPプロキシ・PR・テスト）

- [[01_agent-skill-creator ワークフローをAIスキル化]] — trendtech33566(@trendtech33566)紹介/FrancyJGLisboa作成のOSS(⭐約2千)。自然文の作業フローをSKILL.mdに自動変換・1ファイルで17プラットフォーム対応・検証/セキュリティスキャン/evals付き。「定型作業をAIに覚えさせたい」人向け。[[01_Agent Skillsを作る完全プロンプト]](手書きプロンプト)のツール化（本文はユーザー提供クリップのポストから再構成・詳細はリポジトリ要確認）の要約
- [[02_Playwright業務E2Eテストの統合アーキテクチャ]] — lin-yuchen(Developers.io)によるPlaywright+TypeScriptのE2Eテスト設計。Screen Object Model+Fluent Chaining+日本語メソッド名+ロケーター関数辞書+ポップアップ ファクトリ封じ込めの5パターン統合。「書き方より構造で寿命が決まる」(@connect24h実務共感ポスト統合)。テストコードが仕様書に（本文はユーザー提供クリップから再構成）の要約
- [[03_mcpo MCP-to-OpenAPIプロキシ]] — DanKornas(@DanKornas)紹介/open-webui作成のOSS(MIT)。MCPサーバをOpenAPI互換アプリへ繋ぐプロキシ。OpenAPIスキーマ+インタラクティブドキュメント自動生成。stdio/SSE/Streamable HTTP対応・Claude Desktop形式マルチサーバ・OAuth 2.1（本文はユーザー提供クリップのポストから再構成）の要約
- [[04_GitHub公式スタックPRとgh-stackスキル]] — tonkotsuboy_com(@tonkotsuboy_com)によるGitHub公式スタック型PRとgh-stackスキルの紹介。変更を依存関係のある小さなPRの連鎖に積み重ね、gh-stack Agent Skillで自然言語操作(Claude Code/Codex)。Agent SkillのPR操作実例（本文はユーザー提供クリップから再構成）の要約
- [[05_openai codex-plugin-cc Claude CodeからCodexへ委譲]] — OpenAI公式プラグイン。Claude Code内からCodexを呼び出し/codex:review(読取専用)・/codex:adversarial-review(挑戦的レビュー・方向性を疑う)・/codex:rescue(タスク委譲)。@cursorvers「Claude orchestrator+別モデルworker」を公式に支える道具。adversarial-reviewはcccの査(別モデル検証)の簡易版（本文はユーザー提供クリップのREADMEから再構成）の要約
- [[06_Tsumugu AI時代のZero Config ドキュメントサーバー]] — おく(DevelopersIO)によるドキュメントサーバー(pre-alpha)。`npx tsumugu dev docs` だけでMarkdown/HTML/MDX/OpenAPI を読めるサイトに。Semantic AST で全フォーマット統一・機械向け出力(documents.json/llms.txt/search.json/sitemap.xml)をASTから生成しテーマ差替でAIテキスト不変。セキュリティモデル「Content does not execute」(書き手を信用しない/CSP+SHA-256ハッシュ/MDXは実行しない)。全コードClaude Code/Codex生成・930+テスト（本文はユーザー提供クリップから再構成）の要約

#### 03_RAGと検索と観測（知識グラフ化・視覚RAG・トレーシング）

- [[01_Mastra Trace Intelligence]] — エージェントのトレースをUMAP+HDBSCANで自動クラスタリングし、goal/behavior/sentiment/outcomeの4シグナルで共通パターンを可視化するMastra観測性機能（ベータ）の要約
- [[02_PixelRAG スクショで検索するRAG]] — Web/PDFをスクショ画像のままQwen3-VL-Embedding+FAISSで検索しVLMに読み取らせる「視覚ベースRAG」。テキストRAG比最大+18.1%、Wikipedia事前構築インデックス公開済みの要約
- [[03_graphify コードベースを知識グラフ化]] — Graphify-Labsによる`/graphify`スキル。コード/docs/PDF/画像/動画を知識グラフ化しgrepでなくグラフをクエリ。コードはtree-sitter ASTで完全ローカル・LLM不使用・ベクトル不使用。EXTRACTED/INFERRED信頼度タグ。20+アシスタント対応の要約
- [[04_オントロジーでClaude性能向上 知識グラフの実測]] — @claudecode_labによる知識グラフ(オントロジー)実測編。同じ作業をグラフあり/なしで比較:コスト26%減・応答29%短縮・キャッシュ読取33%減。資料間の「書かれていない関係」をエンティティ/リレーション(型付き)/オブザベーションで明示。導入はmemory MCPサーバー(@modelcontextprotocol/server-memory)で3ステップ・10分。リポジトリ大ほど効果大（本文はユーザー提供クリップから再構成）の要約

#### 04_ツール集とリスト（汎用ツール・OSS代替リスト）

- [[01_Brevio ローカル完結の492種類ツール集]] — WebAssembly/Canvas APIでブラウザ内完結・無料492種ツールを謳うBrevioの紹介記事の要約
- [[02_無料OSSで有料ツールを代替する10のGitHubリポジトリ]] — 有料SaaSを代替できる無料OSSリポジトリ10選の要約

### 03_セキュリティ

- [[01_MIXI新卒技術研修セキュリティ研修まとめ]] — CIAトライアド、アタックサーフェス別対策、セキュアな開発作法をまとめた新卒研修資料の要約

### 04_論文執筆

- [[01_科学論文での図表作成のルール]] — 医学論文の図表作成の作法を全体20＋表20＋図5ルールで体系化した解説（JIS X 4051準拠）の要約
- [[02_段取り八分とリスク・備えの3段階]] — pokamaru3(@pokamaru3)による生産技術のリスクマネジメント作法。段取り八分を土台に不確実性を事前に分かる/推定できる/現物でしか分からないの3つに切り分け、備え方を物の備え/事の備え/後付け可能の3段階から選ぶ。「過剰備えと無備えは同じ判断放棄」。AI直接関係ないが計画と実行の分離・guardrail・過剰設計警告と通底（本文はユーザー提供クリップから再構成）の要約
- [[03_要件定義 基本設計 詳細設計の流れ]] — nyanchu(Zenn・ラクスAC2023)によるソフトウェア開発プロセスの体系復習。要件定義(What・ビジネス)／基本設計(How・IT全体像)／詳細設計(プログラム詳細・エンジニア向け)の3層。機能要件/非機能要件(可用性・性能・拡張性・セキュリティ・移行性・運用保守性)の峻別・要件定義6ステップ。AI直接関係ないがspec駆動・HITL承認・DDDとの対比基準（本文はユーザー提供クリップを構造化）の要約
- [[04_リモートワーカーとしての振る舞い]] — sat(satoru-takeuchi・はてなブログ)による10年フルリモートの実務知見。リモートは難易度がオフィスより高い(コミュニケーション限定ゆえ)。ブラックホールにならず・フロー情報(分報)とストック情報(集中管理)の使い分け・5つの実践。AI直接関係ないが状態可視化・非同期⇄同期切替・log.md/index.mdの2層と通底（本文はユーザー提供クリップから再構成）の要約

### 07_思考・意思決定（確率・定量思考・リスク評価・思考法）

- [[01_Randomnessを測る 定量思考が確率を形にする]] — @0xTrackmindによる定量思考の6側面。ランダムさは理解の敵でなく最後に学ぶ測定対象。中心極限定理(カオスは平均化で秩序)・信号とノイズの分離(Sharpe ratio・小サンプル不信)・キャリブレーション(Kelly criterion・信念を数字に)・産業化された懐疑(out-of-sample検証)・テールリスク(fat tails・VaR・2008根因)。「直感＝迷信を経験のように着せたswap」。AI直接関係ないがエージェント評価・信頼度過信と通底（本文はユーザー提供クリップから再構成）の要約

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
