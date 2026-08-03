---
title: log
tags: [schema]
created: 2026-07-29
---

# log

時系列ログ。**新しい順（降順）** で記載し、新しいエントリは頭（上）に追加する。各エントリに **更新日付＋更新時刻** を付ける。

---

- 2026-08-03 11:30 Ingest: 文字起こし「Sam Altman スタンフォードシステム設計クラス Q&A」を取り込み、[[06_Sam Altman スタンフォード システム設計とスケール]] を作成（01_AI/03_エージェント運用・ガバナンス カテゴリ）。本文はユーザー提供のWSL上の文字起こしファイル(36行)を [[60_…（出典）.txt]] としてコピーし .md 出典ページでメタデータ化。スケールの創発特性(経験的に真・理論なし・YCバッチのネットワーク効果も創発)・ChatGPT誕生秘話(API→チャット使用観察→YC原則「よく使うものをやる」→5日目に緊急宣言→2ヶ月の猛烈スケール)・AIをユーティリティとして売る(電力会社が「電気」でなく「光」を売った比喩・OpenAIも「インテリジェンス」でなく「光」相当を見つける必要)・アイデンティティの罠(信念にアイデンティティを結ぶと真実が見えなくなる)・教育への懸念(3年半で体系的変化なし・予測ミス)・3つの分岐(民主化80% vs 少数集中・分散コンピューティング・市民富裕基金)・計算資源不足は永遠(需要は事実上無限)。※文字起こしは全体に高品質だが18行目に日本語混入の断絶・16-17行目に"light"→"light night"誤変換・質問者とSamの発言混在あり・ASRループ障害(反復)はなし。前回スキップしたFFXIV会議文字起こしとは対照的に品質も内容も問題なし
- 2026-08-03 08:46 Ingest（Clippings溜まり4件＋WSL文字起こし1件＝計5件一括処理）:
  - Zenn記事「AI フレンドリーな CLI を開発するテクニック」(shunsuke_suzuki)を取り込み、[[08_AIフレンドリーなCLIを開発するテクニック]] を作成（01_AI/02_Claude Code実践/03_MCPとスキルとアーキテクチャ カテゴリ）。本文は Clippings/ の全文を使用。AIは知られたOSSでないと知識を持たず・闇雲なWeb Fetchは効率悪く要約で情報落ち。help/ログのagent向けメッセージ・docsコマンドでWeb Fetch不要・Authの人間とagent責務分離・ドキュメントとAgent Skillの共通化・構造化出力。ghtkn実践の一般論
  - Xポスト「Company OS 自律型企業運営のアーキテクチャ」(@ai_hakase_紹介)を取り込み、[[05_Company OS 自律型企業運営のアーキテクチャ]] を作成（01_AI/03_エージェント運用・ガバナンス カテゴリ）。本文は Clippings/ の全文（日本語・動画紹介ポスト）を使用。LLMを企業基幹業務を自律的にこなす「OS」へ。Single Shared State(全エージェント同じ状態参照・不整合物理防止)・Gate(ポリシー違反でApprove→Override強制切替・人間介入)・100万トークン$0.30・9ファイル。※動画ベース紹介ポスト・詳細は動画に依存
  - Xポスト「ループ仕様論文」(@beamnxw紹介/arXiv 2607.00038)を取り込み、[[03_ループ仕様 論文 コーディングエージェントの実行レイヤ]] を作成（01_AI/01_エージェント設計論/02_Loop実践 カテゴリ）。本文は Clippings/ のポスト（日本語＋英語コメント）を使用（※論文本文未取得）。コーディングエージェントのための「欠けていた実行レイヤ」としてループ仕様(明示的トリガー/検証可能目標/停止ルール/メモリの4要素)を定義。手動プロンプティングを境界付き自己修正ループで置換。実世界70%が決定論的チェッカー・終端状態・メイカークリティック分割で自律ゾーン実行。[[02_Loop Engineering Claude,GPT 実戦で効くもの]](実戦5原則)の学術的定式化
  - はてなブログ「リモートワーカーとしての振る舞い」(sat/satoru-takeuchi)を取り込み、[[04_リモートワーカーとしての振る舞い]] を作成（04_論文執筆 カテゴリ・作業論として配置）。本文は Clippings/ の全文を使用。10年フルリモートの実務知見。リモートは難易度がオフィスより高い(コミュニケーション限定ゆえ)。ブラックホールにならず・フロー情報(分報)とストック情報(集中管理)の使い分け。AI直接関係ないがlog.md/index.mdの2層・状態可視化と通底。※既存03_要件定義との番号衝突回避で04_に採番
  - 文字起こし「LangGraphワークショップ 信頼性のあるエージェントの構築とテスト」(LangChainチーム)を取り込み、[[05_LangGraphワークショップ 信頼性のあるエージェントの構築とテスト]] を作成（01_AI/01_エージェント設計論/03_Graph実装 カテゴリ）。本文はユーザー提供のWSL上の文字起こしファイル(931行)を [[59_…（出典）.txt]] としてコピーし .md 出典ページでメタデータ化。Chain vs React agentの中間としてLangGraph。trajectory(ツール呼び出し軌跡)評価でLangGraphはローカルモデルでも一貫性保証・ReactはGPT-4oでも軌跡乱れる。「The model is not the moat」。Corrective RAG・ルーターノード・RAG for tools・indexing/generation分離・loss-in-the-middle。※後半(488行以降)にASRループ障害あり・前半中心に構成
  - 処理済みクリップ4件を Clippings/2026-08-03/ へ移動（Ingest処理日基準）。文字起こしファイルはWSLからraw-sourcesへコピー済みでClippings移動対象外
  - ※作業中に Vault 全体の大規模再編成（01_エージェント設計論・02_Claude Code実践・02_ツール がサブフォルダ構造へ移行済み）を発見。本セッションで作成の3ファイル（15_ループ仕様→02_Loop実践/03_、16_LangGraph→03_Graph実装/05_、15_AIフレンドリーCLI→03_MCPとスキルとアーキテクチャ/08_）を新構造の適切なサブフォルダへ移動・再採番・title更新。03_ガバナンス・04_論文執筆は単一階層のため現状維持（04_論文執筆のみ03_要件定義との衝突回避で04_に）
- 2026-08-02 Ingest（Clippings溜まり4件処理・#1は重複で移動のみ・実質3新規ページ）:
  - X article「Agent Harness Engineering vs Loop Engineering vs Graph Engineering」(@LunarResearcher)は [[04_Agent Harness vs Loop vs Graph Engineering]](01_概念と入門)・出典 [[27_Agent Harness vs Loop vs Graph Engineering（出典）]] として既に取り込み済みのため重複ノート作成回避。処理済みクリップとして Clippings/2026-08-02/ へ移動のみ
  - 処理済みクリップ4件を Clippings/2026-08-02/ へ移動（Ingest処理日基準）
- 2026-08-02 Ingest（Clippings溜まり6件一括処理・#3は既存ノートへ統合で5新規ページ＋1更新）:
  - ※詳細は前回エントリ参照（サブアイテムは省略・必要に応じて git 履歴より復元可）
- 2026-08-02 Query 書き戻し: ccc（Claude-Code-Communication）と同じアプローチの事例調査を [[03_ccc関連事例調査 ボルト内の同じアプローチ]]（02_wiki/projects/）へ作成。ccc の8軸（マルチエージェント協調・チケット駆動・階層的 orchestrator-worker・ハーネス基盤・エージェント間通信・CI/CD・HITL/権限分離・Graph/Loop/Harness 検証器）について、ボルト内から tier1（4件）・tier2（5件）・tier3（3件）＋補完ノード群を抽出。伝言ゲーム対策（Anthropic 自己レビューメソッド Step6・Graph of Loops「doneを取り戻せるか」）・権限の構造的強化（Opus5 12ステップ・エージェントファクトリ）・段階的移行の理論的根拠（24時間自走・ファクトリ）が最も応用可能
- 2026-08-02 Ingest: 講演音声書き起こし「Boris Cherny 講演（Claude Code リードエンジニア @ Anthropic）」を取り込み、[[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]] を作成（01_AI/02_Claude Code実践/03_MCPとスキルとアーキテクチャ カテゴリ）。本文はユーザー提供トランスクリプト（tc/output/20260802_111544）から主題別に再構成。モデル毎にシステムプロンプト80%削除・ハーネス一から再構築・simple mode（`CLAUDE_CODE_SIMPLE=1`）。product overhang / unhobbling（Claude Code 自身の誕生秘話）。Bun の Zig→Rust 書き換え11日・プロダクション採用。Boris 自身の Electron→Swift 書き換え15日以上実行中。dynamic workflows（新しいテスト時計算）と loops/routines で Claude Code 自身を自律保守（デッドコード掃除・abstraction please 等・毎日数千エージェント）。「同僚のように扱う・過剰指定しない・LinkedIn インフルエンサーを読むな・経験主義・先入観を捨てる」。学生へは「実践から入り自分が欲しいもの→人々が欲しいもの」。本ボルト運用ルール（CLAUDE.md・ハーネス設計）の根拠を揺さぶる重要知見（モデルが賢くなったら CLAUDE.md も削除して試す）
- 2026-08-02 Restructure: `02_ツール`(13ノート) を更に細分化。4サブフォルダへ分割し、フォルダ内で01_から再採番。
- 2026-08-02 Restructure: `01_エージェント設計論`(15ノート)と `02_Claude Code実践`(18ノート) を更に細分化。各4サブフォルダへ分割し、フォルダ内で01_から再採番。
- 2026-08-02 Ingest（Clippings溜まり7件一括処理・#1は既存出典の原本差替で実質6新規ページ＋1更新）:
  - X article「Agent Harness Engineering vs Loop Engineering vs Graph Engineering」(@LunarResearcher)は既に [[04_Agent Harness vs Loop vs Graph Engineering]] として取り込み済み。出典ページ [[27_Agent Harness vs Loop vs Graph Engineering（出典）]] は当初ログイン壁のため解説記事群から再構成していたが、今回ユーザーが Clippings/ にクリップした**著者原文全文**を入手したため、原本保存版に差し替え。frontmatter title のプレフィックスズレ(21_→27_)も修正。要約ページ08_に原文で判明した新要素（Environment→Feedback→Flow / Harness 6要素 / Loop 8構成要素 / 「Loop on evidence」最重要原則 / 診断ルール / 5つの間違い / チェックリスト）を反映し、本文取得状況節を更新。重複ノート作成は回避
- 2026-08-01 Ingest（Clippings溜まり8件一括処理・#6は#2へ統合で実質7新規ページ）:
  - ※詳細は前回エントリ参照（サブアイテム多数・省略・git 履歴より復元可）
- 2026-08-01 Ingest（Clippings新規4件一括処理）:
  - ※詳細は前回エントリ参照（サブアイテム多数・省略・git 履歴より復元可）
- 2026-08-01 Restructure（Clippings運用ルール変更）: 処理済みクリップの退避先を `99_to_delete/` から **Clippings/ 内の処理日（Ingest実行日）ベースの日付フォルダ** へ変更。`99_to_delete/Clippings_処理済み_20260801/` の4件を Clippings/2026-08-01/（hi_bysirも処理日基準で同フォルダ）へ戻し、空退避フォルダを削除。未処理クリップは Clippings/ ルート直下に置き、Ingest時にその日のフォルダへ移動する運用に固定
- 2026-08-01 Ingest: Xポスト「How to Become a Graph Architect With Zero Experience (Full Course)」(@eng_khairallah1)を取り込み、[[04_Graph Architectへの20ステップ5フェーズ]] を作成（01_AI/01_エージェント設計論 カテゴリ）。本文は Clippings/ の全文（英語）を使用。同著者 [[02_Loop Engineering Claude,GPT 実戦で効くもの]] の延長線上にある学習ロードマップ。Phase1 Loop必習(verifierが全て/4失敗モード)→Phase2 グラフモデル(3プリミティブ/全ノードがLLMでない/LangGraph)→Phase3 5パターン(router/orchestrator-worker/fan-out fan-in/evaluator-optimizer/HITL gate)→Phase4 信頼性(validation gate/リカバリ/state永続化/観測可能性)→Phase5「グラフにしない」判断。[[03_Graph Engineering with Claude 14-Step roadmap]](@0xCodez・実装完全マニュアル)の対で学習の道筋。Step19(グラフにしない)は [[02_Graph Engineering 最大の間違い Loop↔Graph判断]] と同系、Step14(HITL gate)は [[03_AI協業の発注の型 HITL実務]] のシステム側、Phase4は [[02_24時間自走する自律型AIエージェントの設計図]] と通底。既存ページ2件(09_Loop Engineering/12_Graph 14-Step)の関連欄へ逆リンク追記。処理済みクリップを Clippings/2026-08-01/ へ移動
- 2026-08-01 Ingest: Xポスト「Graph Engineering with Claude: 14-Step roadmap from 0 to graph architect (Full Course)」(@0xCodez)を取り込み、[[03_Graph Engineering with Claude 14-Step roadmap]] を作成（01_AI/01_エージェント設計論 カテゴリ）。本文は Clippings/ の全文（英語）を使用。同著者 [[01_ループエンジニアリング14ステップ]](Loop版)の対であるGraph版。直線=退化グラフ、「次ステップは前の出力を読むか?」で無駄待ちを切る。14手(node契約/schema・edgeはデータ契約・parallel fan-out・barrier fan-in・diamond split→work→merge・runtime router・verifier 3パターン・worktree隔離・loop-until-dryで見たもの全てにdedupe・model tier・pipeline優先・dynamic workflows自己ルーティング)。Claude Code実装完全マニュアル。Graph系既存ノート群(概念/判定/検証/隔離/収束)の頂点。既存ページ2件(05_ループ14ステップ/10_Graph最大の間違い)の関連欄へ逆リンク追記。処理済みクリップを Clippings/2026-08-01/ へ移動
- 2026-08-01 Restructure: `01_AI/`（29ノート）を4サブフォルダへ細分化・再採番。`01_エージェント設計論`(11) / `02_Claude Code実践`(10) / `03_エージェント運用・ガバナンス`(2) / `04_LLM・RAG・基礎`(6)。各フォルダ内で01_から再採番。全Wikilink（完全名66ファイル＋省略記法11ファイル）・frontmatter title（27件、従来のコロン表記ズレ6件も統一）を新ファイル名へ追従。バックアップ `_prefix_backup_20260801/01_AI/` 作成済み。index.md の01_AI欄を4サブフォルダ構造へ再構成、CLAUDE.md にサブフォルダ規約を追記
- 2026-08-01 Ingest（Clippings新規5件一括処理）:
  - Xポスト「Vibe Kanban」(@DanKornas)を取り込み、[[01_Vibe Kanban コーディングエージェント用Kanbanワークスペース]] を作成（02_ツール カテゴリ）。本文は Clippings/ の全文を使用。エージェントごとのブランチ/ターミナル/開発サーバー分離＋差分レビュー。※README にてサービス終了予定と注記
  - Xポスト「agmsg v1.1.12」(@fujibee)を取り込み、[[02_agmsg CLIエージェント間メッセージング]] を作成（02_ツール カテゴリ）。本文は Clippings/ の全文を使用。CLI エージェント間メッセージング OSS、v1.1.12 はコミュニティ主導の成熟(opencode-sentinel/codexブリッジ修正/可視化ツール5つ)
  - ウェブ記事「モデルを探すコつとおすすめタグ」(AI難民キャンプ)を取り込み、[[06_Hugging Faceでモデルを探すコツ]] を作成（01_AI カテゴリ）。※クリップファイルに本文なく frontmatter＋URL のみ、description 基づく構成推定と明記
  - Xポスト「A Graph of Loops」(@Granite0x)を取り込み、[[03_Graph of Loops Claude Code完全システム10リポジトリ]] を作成（01_AI カテゴリ）。本文は Clippings/ の全文（英語）を使用。Graph(G1-G4)+Loop(L1-L6)の10実リポジトリをソースコードレベルで解説。「doneを取り戻せるか」が核心。bernstein/agent-worktree/wshobson-agents/beads/serena/superpowers 等
  - GitHub README「graphify」(Graphify-Labs)を取り込み、[[03_graphify コードベースを知識グラフ化]] を作成（02_ツール カテゴリ）。本文は Clippings/ の README 全文を使用。`/graphify` スキルでコードベースを知識グラフ化、tree-sitter AST で完全ローカル・LLM不使用・ベクトル不使用、EXTRACTED/INFERRED 信頼度タグ。[[02_GraphRAG 知識グラフでRAGを置き換える]] のコード特化実装として位置付け
- 2026-07-31 Ingest: Xポスト「【完全版】24時間自走する自律型AIエージェントの設計図」(@aitech_jp)を取り込み、[[02_24時間自走する自律型AIエージェントの設計図]] を作成（01_AI カテゴリ）。本文はユーザーが Clippings/ にクリップした全文（日本語）を使用。4層(Trigger/Workflow/Agent/Guardrail)・状態機械優先・7原則・5実行環境・15チェック・設計プロンプト。既存 [[02_Loop Engineering Claude,GPT 実戦で効くもの]]・[[01_エージェントファクトリの作り方 ビルダーズガイド]]・[[02_Graph Engineering 最大の間違い Loop↔Graph判断]] と運用系譜で補完
- 2026-07-31 Ingest: Xポスト「How to Build Your First Agent Factory (Builder's Guide)」(@Av1dlive, 約5400語)を取り込み、[[01_エージェントファクトリの作り方 ビルダーズガイド]] を作成（01_AI カテゴリ）。本文はユーザーが Clippings/ にクリップした全文（英語）を使用。5ステーション・第2の首機械化・6テスト・7ガード・7日ロードマップ。Sage API紹介色強いが工場思想は製品非依存と注記
- 2026-07-31 Ingest: Xポスト「The biggest Graph Engineering mistake everyone makes」(@wandermist)を取り込み、[[02_Graph Engineering 最大の間違い Loop↔Graph判断]] を作成（01_AI カテゴリ）。本文はユーザーが Clippings/ にクリップした全文（英語）を使用。「最大の間違い＝必要でないグラフ作成」。5シグナル・決定木・6チェックリストでLoop↔Graph判定
- 2026-07-31 Update: X記事「LOOP vs GRAPH vs HARNESS ENGINEERING」(@0xwhrrari)は既に [[01_LOOP vs GRAPH vs HARNESS ENGINEERING]] として取り込み済みだったが、出典ページ [[08_LOOP vs GRAPH vs HARNESS ENGINEERING（出典）]] は本文未保存のスタブだった。ユーザーが Clippings/ にクリップした完全な原文を入手したため、出典ページを全文保存版に書き換え、要約ページに出典全文が揃った旨を追記（重複ノート作成は回避）
- 2026-07-31 Ingest: Xポスト「How to Turn One Chat Into a Team of Agents: The 12-Step Opus 5 Course」(@0x_rody)を取り込み、[[02_1チャットをエージェントチームへ Opus5 12ステップ]] を成（01_AI カテゴリ）。本文はユーザーが Clippings/ にクリップした全文（英語）を使用。サブエージェントの12ステップ・モデル/effortルーティング・構造的深さキャップ・検証器を網羅
- 2026-07-31 Ingest: Xポスト「Loops Engineering: Claude, GPT, and What Actually Works」(@eng_khairallah1)を取り込み、[[02_Loop Engineering Claude,GPT 実戦で効くもの]] を作成（01_AI カテゴリ）。本文はユーザーが Clippings/ にクリップした全文（英語）を使用。Loop vs Chain・Claude/GPT収束・実戦5原則・4罠を網羅
- 2026-07-31 Ingest: X article「Claude Code×Obsidianで第二の脳を作る方法」(@ClaudeCode_aca)を取り込み、[[02_Claude Code×Obsidianで第二の脳を作る 2000時間の設計ルール]] を作成（01_AI カテゴリ）。本文はX articleログイン壁のため、ユーザーが Clippings/ にクリップした全文を使用。既存 [[01_Claude×Obsidianで第二の脳を作る]] の実践版
- 2026-07-31 Ingest: X article「Agent Harness Engineering vs Loop Engineering vs Graph Engineering」(@LunarResearcher)を取り込み、[[04_Agent Harness vs Loop vs Graph Engineering]] を作成（01_AI カテゴリ）。本文はX articleログイン壁で取得不可のため複数独立解説から再構成
- 2026-07-31 Ingest: デジタル庁PDF「ChatGPTを業務に組み込むためのハンズオン」を取り込み、[[05_デジタル庁 ChatGPTを業務に組み込むためのハンズオン]] を作成（01_AI カテゴリ）。pdftotext で和文化けしたため PyMuPDF で抽出
- 2026-07-31 Ingest: Xポスト「PixelRAG スクショで検索するRAG」(@cosmos_hzokujin)を取り込み、[[02_PixelRAG スクショで検索するRAG]] を作成（02_ツール カテゴリ）
- 2026-07-30 Ingest: X article「Graph Engineering Clearly Explained」(@akshay_pachaar)を取り込み、[[02_Graph Engineering Clearly Explained]] を作成。本文はXログイン壁で直接取得不可のため description・直接引用・解説記事群から再構成。前編 [[02_Prompt to Graph Engineering 5層の統一モデル]] に続編リンクを追記
- 2026-07-30 Ingest: Xポスト「Announcing Trace Intelligence」(@calcsam/Mastra)を取り込み、[[01_Mastra Trace Intelligence]] を作成
- 2026-07-30 Ingest: Xポスト「Context Engineering: Claude Codeの文脈設計」(@hanakoxbt)を取り込み、[[06_Context Engineering Claude Codeの文脈設計]] を作成。@0xkkai のKarpathy記事はXログイン壁で本文取得不可のためスキップ（ユーザー指示）
- 2026-07-30 Ingest: Xポスト「Claude Code 超初心者ボルション道場」(@N01ennn)を取り込み、[[01_Claude Code 超初心者ボルション道場]] を作成
- 2026-07-30 Ingest: Xポスト「AIエージェントの正体はプロンプトだった」(@oda_nobunaga10)を取り込み、[[03_AIエージェントの正体はプロンプトだった]] を作成
- 2026-07-30 Ingest: Xポスト「Claude Code 8時間を1時間にする10の方法」(@0xCodila)を取り込み、[[03_Claude Code 8時間を1時間にする10の方法]] を作成
- 2026-07-30 Ingest: Xポスト「Claude Code 計画と実行を分けるワークフロー」(@S0N_IA)を取り込み、[[02_Claude Code 計画と実行を分けるワークフロー]] を作成
- 2026-07-30 Restructure: 残り5ファイル（raw-sources について/Wikilink/フロントマター/2026-07-28/Obsidianの基本操作）にもプレフィックス付与。例外なく全ファイルを番号付きに統一
- 2026-07-30 Restructure: ノートファイル名にも各フォルダ内通し番号のプレフィックス付与（34ファイル）。全Wikilink・frontmatter title: を新ファイル名へ一括追従。バックアップ `_prefix_backup_20260730_083136/` 作成済み
- 2026-07-30 Restructure: フォルダへ数字プレフィックス付与。トップレベル `raw-sources→01_raw-sources` / `wiki→02_wiki` / `_to_delete→99_to_delete`、summaries `AI→01_AI` / `ツール→02_ツール` / `セキュリティ→03_セキュリティ` / `論文執筆→04_論文執筆`。空フォルダ5つを `99_to_delete/` へ退避。index.md と CLAUDE.md を新構成に更新
- 2026-07-30 Ingest: Xポスト「Agent Skillsを作る完全プロンプト」(@ai_ai_ailover)を取り込み、[[01_Agent Skillsを作る完全プロンプト]] を作成
- 2026-07-30 Ingest: J-STAGE記事「科学論文での図表作成のルール」を取り込み、新カテゴリ`論文執筆`を設けて [[01_科学論文での図表作成のルール]] を作成
- 2026-07-29 Ingest: Xポスト「Claude Code生みの親「ボリス」がオススメする最強のMCPサーバー8選」(@kimuai08)を取り込み、[[02_Claude Code開発者ボリス推奨 MCPサーバー8選]] を作成
- 2026-07-29 Ingest: Xポスト「LOOP → GRAPH → HARNESS: build the whole pipeline in one sitting」(@ArchiveExplorer)を取り込み、[[01_LOOP→GRAPH→HARNESSパイプラインを一気通貫で実装する]] を作成
- 2026-07-29 Ingest: Xポスト「Brevio 492種の無料ツール」(@ai_hakase_)を取り込み、[[01_Brevio ローカル完結の492種類ツール集]] を作成
- 2026-07-29 Restructure: [[02_無料OSSで有料ツールを代替する10のGitHubリポジトリ]] を wiki/summaries/AI から新設の wiki/summaries/ツール へ移動。CLAUDE.md にカテゴリ`ツール`を追記
- 2026-07-29 Ingest: Xポスト「33 Free GitHub Repositories...」(@unicodef1wn)を取り込み、[[02_無料OSSで有料ツールを代替する10のGitHubリポジトリ]] を作成
- 2026-07-29 Ingest: Xポスト「Prompt→Context→Harness→Loop→Graph Engineering」(@akshay_pachaar)を取り込み、[[02_Prompt to Graph Engineering 5層の統一モデル]] を作成
- 2026-07-29 Ingest: GitHubリリース「J-Tech-Japan/intent-system v0.6.0」を取り込み、[[01_intent-system 意図駆動開発のオーケストレーションCLI]] を作成
- 2026-07-29 Ingest: X記事「Graph Engineering replaced RAG...」(@Sprytixl)を取り込み、[[02_GraphRAG 知識グラフでRAGを置き換える]] を作成
- 2026-07-29 Ingest: X記事「Loop engineering: the 14-step roadmap」(@0xCodez)を取り込み、[[01_ループエンジニアリング14ステップ]] を作成
- 2026-07-29 Ingest: X記事「How to Do Graph Engineering With Opus 5」(@0x_rody)を取り込み、[[01_知識グラフメモリをOpus5で安く運用する]] を作成
- 2026-07-29 Ingest: GitHubリポジトリ「langchain-ai/langchain」を取り込み、[[03_LangChain エージェント・エンジニアリング・プラットフォーム]] を作成
- 2026-07-29 Ingest: GitHubリポジトリ「langflow-ai/langflow」を取り込み、[[04_Langflow ビジュアルAIワークフロービルダー]] を作成
- 2026-07-29 Ingest: X記事「Graph Engineering: How to Stop Building AI Agents That Wait in Line」(@mikenevermiss)を取り込み、[[01_Graph Engineering エージェントを行列から解放する]] を作成
- 2026-07-29 Ingest: X記事「LOOP vs GRAPH vs HARNESS ENGINEERING」(@0xwhrrari)を取り込み、[[01_LOOP vs GRAPH vs HARNESS ENGINEERING]] を作成
- 2026-07-29 Restructure: wiki/summaries/ をカテゴリ別サブフォルダ（AI・セキュリティ）に分類。CLAUDE.md に分類規約を追記
- 2026-07-29 Ingest: SpeakerDeck資料「セキュリティ研修【MIXI 26新卒技術研修】」を取り込み、[[01_MIXI新卒技術研修セキュリティ研修まとめ]] を作成
- 2026-07-29 Restructure: ボルトを3層構造（raw-sources / wiki / schema）に再編。CLAUDE.md・log.md・index.md を新設
- 2026-07-29 Ingest: 記事「Claude×Obsidian 海外で『良すぎるから違法にしろ』と言われた第二の脳の作り方」を取り込み、[[01_Claude×Obsidianで第二の脳を作る]] を作成
- 2026-07-28 Setup: ボルト作成。テストノート6件作成（インデックス／使い方／プロジェクト／デイリーノート／用語集2件）
