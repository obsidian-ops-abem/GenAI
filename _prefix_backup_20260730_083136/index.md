---
title: インデックス
aliases: ["00_インデックス"]
tags: [moc]
created: 2026-07-28
updated: 2026-07-30
---

# インデックス

このボルトの入口。3層構造については [[CLAUDE]] を、更新履歴は [[log]] を参照。

## ボルト構成（数字プレフィックス順）

| フォルダ | 役割 |
|---|---|
| `01_raw-sources/` | 加工前の生資料。読むだけ・書き換えない。詳細は [[raw-sources について]] |
| `02_wiki/` | Claude が生成・維持するページ群（下記） |
| `99_to_delete/` | 削除候補の退避先（環境上の削除不可のため移動で代用） |

---

## 記事まとめ (`02_wiki/summaries/`)

### 01_AI

- [[Claude×Obsidianで第二の脳を作る]] — RAGとの違い、3層構造、Ingest/Query/Lintの3操作を解説した記事の要約
- [[_prefix_backup_20260730_083136/02_wiki/summaries/01_AI/LOOP vs GRAPH vs HARNESS ENGINEERING]] — AIエージェント設計をHarness/Loop/Graphの3層で診断するフレームの要約
- [[LOOP→GRAPH→HARNESSパイプラインを一気通貫で実装する]] — 同じ3層フレームをコード実装まで一気通貫でやってみせるチュートリアルの要約
- [[Claude Code開発者ボリス推奨 MCPサーバー8選]] — Boris Chernyの3原則を軸に、Context7/Playwright/Figma等の公式MCPサーバー8選を紹介する記事の要約
- [[Graph Engineering エージェントを行列から解放する]] — ノード/エッジ/6トポロジーでエージェントをグラフとして設計する実践ガイドの要約
- [[Langflow ビジュアルAIワークフロービルダー]] — AIワークフローをビジュアルに組みAPI/MCPサーバーとしてデプロイできるOSSの要約
- [[LangChain エージェント・エンジニアリング・プラットフォーム]] — LLMアプリを組むための基盤ライブラリ群（Core/Deep Agents/LangGraph）の要約
- [[知識グラフメモリをOpus5で安く運用する]] — 時系列知識グラフの取り込みコストをOpus5のキャッシュ/バッチで下げる手法の要約
- [[ループエンジニアリング14ステップ]] — プロンプターからループ設計者への14ステップ・3段階ロードマップの要約
- [[GraphRAG 知識グラフでRAGを置き換える]] — 通常のRAGを知識グラフ（GraphRAG）で置き換える手法とその根拠論文の要約
- [[intent-system 意図駆動開発のオーケストレーションCLI]] — GitHub上でAIエージェントの作業を状態管理するCLIツール v0.6.0の要約
- [[Prompt to Graph Engineering 5層の統一モデル]] — Prompt/Context/Harness/Loop/Graphの5層を「作業単位」で統一するフレームの要約
- [[Agent Skillsを作る完全プロンプト]] — AI自身に逆質問させてSKILL.mdを書かせる対話型プロンプトと、Skill運用の作法をまとめた35万PV超のガイドの要約

### 02_ツール

- [[無料OSSで有料ツールを代替する10のGitHubリポジトリ]] — 有料SaaSを代替できる無料OSSリポジトリ10選の要約
- [[Brevio ローカル完結の492種類ツール集]] — WebAssembly/Canvas APIでブラウザ内完結・無料492種ツールを謳うBrevioの紹介記事の要約

### 03_セキュリティ

- [[MIXI新卒技術研修セキュリティ研修まとめ]] — CIAトライアド、アタックサーフェス別対策、セキュアな開発作法をまとめた新卒研修資料の要約

### 04_論文執筆

- [[科学論文での図表作成のルール]] — 医学論文の図表作成の作法を全体20＋表20＋図5ルールで体系化した解説（JIS X 4051準拠）の要約

---

## プロジェクト (`02_wiki/projects/`)

- [[認証基盤リプレース]] — 自前セッション管理をOIDCベースへ移行するプロジェクト

## デイリーノート (`02_wiki/daily/`)

- [[2026-07-28]] — ボルト作成日の記録

## 用語集 (`02_wiki/concepts/`)

- [[Wikilink]]
- [[フロントマター]]

## 使い方 (`02_wiki/howto/`)

- [[Obsidianの基本操作]]

## 資料庫

- `01_raw-sources/` — 加工前の生資料を置く場所。詳細は [[raw-sources について]]
