## 目的
`02_wiki/summaries/01_AI/` 配下の29ノートが増えすぎたため、テーマ別に4サブフォルダへ細分化し、各フォルダ内で 01_ から再採番する。

## 方針（ユーザー承認済み）
- テーマ別4分割
- フォルダ内で再採番（ファイル名プレフィックスを新フォルダの連番に変更）
- CLAUDE.md 警告に従い、**全 .md 内の Wikilink と frontmatter title: を新ファイル名へ手動追従**

## 分類と新番号マッピング（29ノート）

### `01_AI/01_エージェント設計論/`（11ノート — Harness/Loop/Graph の概念・理論）
| 旧 | 新 |
|---|---|
| 04_Graph Engineering エージェントを行列から解放する | 01_Graph Engineering エージェントを行列から解放する |
| 06_LOOP vs GRAPH vs HARNESS ENGINEERING | 02_LOOP vs GRAPH vs HARNESS ENGINEERING |
| 07_LOOP→GRAPH→HARNESSパイプラインを一気通貫で実装する | 03_LOOP→GRAPH→HARNESSパイプラインを一気通貫で実装する |
| 10_Prompt to Graph Engineering 5層の統一モデル | 04_Prompt to Graph Engineering 5層の統一モデル |
| 12_ループエンジニアリング14ステップ | 05_ループエンジニアリング14ステップ |
| 16_AIエージェントの正体はプロンプトだった | 06_AIエージェントの正体はプロンプトだった |
| 19_Graph Engineering Clearly Explained | 07_Graph Engineering Clearly Explained |
| 21_Agent Harness vs Loop vs Graph Engineering | 08_Agent Harness vs Loop vs Graph Engineering |
| 23_Loop Engineering Claude,GPT 実戦で効くもの | 09_Loop Engineering Claude,GPT 実戦で効くもの |
| 25_Graph Engineering 最大の間違い Loop↔Graph判断 | 10_Graph Engineering 最大の間違い Loop↔Graph判断 |
| 29_Graph of Loops Claude Code完全システム10リポジトリ | 11_Graph of Loops Claude Code完全システム10リポジトリ |

### `01_AI/02_Claude Code実践/`（10ノート — Claude Code の使い方・ワークフロー・MCP・スキル）
| 旧 | 新 |
|---|---|
| 01_Agent Skillsを作る完全プロンプト | 01_Agent Skillsを作る完全プロンプト |
| 02_Claude Code開発者ボリス推奨 MCPサーバー8選 | 02_Claude Code開発者ボリス推奨 MCPサーバー8選 |
| 11_intent-system 意図駆動開発のオーケストレーションCLI | 03_intent-system 意図駆動開発のオーケストレーションCLI |
| 13_知識グラフメモリをOpus5で安く運用する | 04_知識グラフメモリをOpus5で安く運用する |
| 14_Claude Code 計画と実行を分けるワークフロー | 05_Claude Code 計画と実行を分けるワークフロー |
| 15_Claude Code 8時間を1時間にする10の方法 | 06_Claude Code 8時間を1時間にする10の方法 |
| 17_Claude Code 超初心者ボルション道場 | 07_Claude Code 超初心者ボルション道場 |
| 18_Context Engineering Claude Codeの文脈設計 | 08_Context Engineering Claude Codeの文脈設計 |
| 22_Claude Code×Obsidianで第二の脳を作る 2000時間の設計ルール | 09_Claude Code×Obsidianで第二の脳を作る 2000時間の設計ルール |
| 24_1チャットをエージェントチームへ Opus5 12ステップ | 10_1チャットをエージェントチームへ Opus5 12ステップ |

### `01_AI/03_エージェント運用・ガバナンス/`（2ノート — 長時間運用・ファクトリ・品質保証）
| 旧 | 新 |
|---|---|
| 26_エージェントファクトリの作り方 ビルダーズガイド | 01_エージェントファクトリの作り方 ビルダーズガイド |
| 27_24時間自走する自律型AIエージェントの設計図 | 02_24時間自走する自律型AIエージェントの設計図 |

### `01_AI/04_LLM・RAG・基礎/`（6ノート — LLM基礎・RAG・モデル選定・プラットフォーム）
| 旧 | 新 |
|---|---|
| 03_Claude×Obsidianで第二の脳を作る | 01_Claude×Obsidianで第二の脳を作る |
| 05_GraphRAG 知識グラフでRAGを置き換える | 02_GraphRAG 知識グラフでRAGを置き換える |
| 08_LangChain エージェント・エンジニアリング・プラットフォーム | 03_LangChain エージェント・エンジニアリング・プラットフォーム |
| 09_Langflow ビジュアルAIワークフロービルダー | 04_Langflow ビジュアルAIワークフロービルダー |
| 20_デジタル庁 ChatGPTを業務に組み込むためのハンズオン | 05_デジタル庁 ChatGPTを業務に組み込むためのハンズオン |
| 28_Hugging Faceでモデルを探すコツ | 06_Hugging Faceでモデルを探すコツ |

## 実行ステップ

1. **バックアップ作成**: `_prefix_backup_20260801/` へ 01_AI/ 全体をコピー（既存バックアップと同様の安全策）
2. **4サブフォルダ作成**: `01_エージェント設計論/` `02_Claude Code実践/` `03_エージェント運用・ガバナンス/` `04_LLM・RAG・基礎/`
3. **29ファイルを新フォルダへ新ファイル名で作成**（Write で新ファイル作成 → 旧ファイル削除）。各ファイルで:
   - frontmatter `title:` を新ファイル名（プレフィックス含む）に一致させる（04,05,10,11,12,23 の6件は現状 title がズレているため、この機に統一）
   - ファイル内の他ノートへの Wikilink は「ファイル名（プレフィックス含む）」記法のため、新ファイル名へ更新
4. **全参照元ファイルの Wikilink追従**（影響範囲: index.md, log.md, CLAUDE.md, 01_raw-sources/ の出典ファイル群38件, 02_wiki/summaries/ の他カテゴリノード）:
   - 旧ファイル名 → 新ファイル名へ一括置換
   - 省略記法 `[[NN_...]]` も新番号へ更新
5. **index.md の構造更新**: 01_AI 欄を4サブフォルダ構造へ再構成
6. **log.md へ Restructure 履歴追記**: `- 2026-08-01 Restructure: 01_AI/ を4サブフォルダ（01_エージェント設計論/02_Claude Code実践/03_エージェント運用・ガバナンス/04_LLM・RAG・基礎）へ細分化・再採番。全Wikilink・title追従`
7. **空になった旧 01_AI/ 直下の29ファイルを削除**（新フォルダへ移動済みのため）

## リスクと安全策
- **取り消し困難な大規模リネーム**のため、ステップ1で丸ごとバックアップを先に作成
- Wikilink追従は置換ミスを防ぐため、旧→新の対応表を厳密に適用（特に省略記法 `[[NN_...]]` と完全名の両方を処理）
- frontmatter title のズレ（6件）もこの機に修正し、CLAUDE.md 規約へ完全準拠
- 実行後、念のため残存する旧ファイル名への参照がないか grep で検証