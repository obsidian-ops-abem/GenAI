---
title: インデックス
aliases: ["00_インデックス"]
tags: [moc]
created: 2026-07-28
updated: 2026-08-01
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

#### 03_エージェント運用・ガバナンス（長時間運用・ファクトリ・品質保証）

- [[01_エージェントファクトリの作り方 ビルダーズガイド]] — Av1dlive(@Av1dlive)によるAgent Factory完全構築ガイド(約5400語)。5ステーション(ABOM/組立/証明/認証/運用)・「第2の首」機械化・6テスト・7ガード・1法(no evals, no production)・4自律ティア・7日ロードマップ。Sage API紹介色強いが工場思想は製品非依存（本文はユーザー提供クリップから再構成）の要約
- [[02_24時間自走する自律型AIエージェントの設計図]] — AIテック(@aitech_jp)による長時間運用エージェントの日本語実務ガイド。4層(Trigger/Workflow/Agent/Guardrail)・「状態機械を先に」・5つの壊れる理由・7設計原則・5実行環境(/loop/Routines/Modal・Trigger.dev/Agent SDK/Managed Agents)・15チェック・設計プロンプト付き（本文はユーザー提供クリップから再構成）の要約

#### 04_LLM・RAG・基礎（LLM基礎・RAG・モデル選定・プラットフォーム）

- [[01_Claude×Obsidianで第二の脳を作る]] — RAGとの違い、3層構造、Ingest/Query/Lintの3操作を解説した記事の要約
- [[02_GraphRAG 知識グラフでRAGを置き換える]] — 通常のRAGを知識グラフ（GraphRAG）で置き換える手法とその根拠論文の要約
- [[03_LangChain エージェント・エンジニアリング・プラットフォーム]] — LLMアプリを組むための基盤ライブラリ群（Core/Deep Agents/LangGraph）の要約
- [[04_Langflow ビジュアルAIワークフロービルダー]] — AIワークフローをビジュアルに組みAPI/MCPサーバーとしてデプロイできるOSSの要約
- [[05_デジタル庁 ChatGPTを業務に組み込むためのハンズオン]] — デジタル庁(2023/06)の非エンジニア向けGPT API実践ガイド。ブラックボックス相手の心構え・messages構成・プロンプトTips・他システム連携・従来NLPとの使い分けを網羅（全46ページ）の要約
- [[06_Hugging Faceでモデルを探すコツ]] — AI難民キャンプによる初心者向けHugging Faceモデル探索ガイド（目的別・日本語対応・おすすめタグ）。※本文未取得、description基づく構成推定の要約

### 02_ツール

- [[01_Brevio ローカル完結の492種類ツール集]] — WebAssembly/Canvas APIでブラウザ内完結・無料492種ツールを謳うBrevioの紹介記事の要約
- [[02_無料OSSで有料ツールを代替する10のGitHubリポジトリ]] — 有料SaaSを代替できる無料OSSリポジトリ10選の要約
- [[03_Mastra Trace Intelligence]] — エージェントのトレースをUMAP+HDBSCANで自動クラスタリングし、goal/behavior/sentiment/outcomeの4シグナルで共通パターンを可視化するMastra観測性機能（ベータ）の要約
- [[04_PixelRAG スクショで検索するRAG]] — Web/PDFをスクショ画像のままQwen3-VL-Embedding+FAISSで検索しVLMに読み取らせる「視覚ベースRAG」。テキストRAG比最大+18.1%、Wikipedia事前構築インデックス公開済みの要約
- [[05_Vibe Kanban コーディングエージェント用Kanbanワークスペース]] — DanKornas(@DanKornas)によるコーディングエージェント向けKanbanワークスペース(BloopAI/vibe-kanban)。エージェントごとにブランチ/ターミナル/開発サーバーを分離し差分レビューをワークスペース内に閉じ込める。※サービス終了予定の要約
- [[06_agmsg CLIエージェント間メッセージング]] — fujibee(@fujibee)によるCLI AIエージェント間メッセージングOSS(Claude Code/Codex/opencode)。v1.1.12はコミュニティ主導の成熟(opencode-sentinel/codexブリッジ修正/可視化ツール5つ)を示すの要約
- [[07_graphify コードベースを知識グラフ化]] — Graphify-Labsによる`/graphify`スキル。コード/docs/PDF/画像/動画を知識グラフ化しgrepでなくグラフをクエリ。コードはtree-sitter ASTで完全ローカル・LLM不使用・ベクトル不使用。EXTRACTED/INFERRED信頼度タグ。20+アシスタント対応の要約

### 03_セキュリティ

- [[01_MIXI新卒技術研修セキュリティ研修まとめ]] — CIAトライアド、アタックサーフェス別対策、セキュアな開発作法をまとめた新卒研修資料の要約

### 04_論文執筆

- [[01_科学論文での図表作成のルール]] — 医学論文の図表作成の作法を全体20＋表20＋図5ルールで体系化した解説（JIS X 4051準拠）の要約

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
