## 概要

`Clippings/` に残る**未処理クリップ4本**を CLAUDE.md の Ingest ルールに従って一括処理する。既存13本のうち9本は既に処理済みで Clippings に残置されているだけ（今回は触らない）。

## 処理対象4本と配置先

| # | クリップ | 著者 | 配置先（要約ページ） | 出典ページ |
|---|---|---|---|---|
| 1 | `Post by @hi_bysir on X`（スマホ→PC リモート操作） | @hi_bysir | `02_ツール/08_スマホからPCのcodexとccをリモート操作` | `01_raw-sources/39_…（出典）` |
| 2 | `Ephemeral-AI-Lab/ephemeral-sandbox` | Ephemeral-AI-Lab | `02_ツール/09_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤` | `01_raw-sources/40_…（出典）` |
| 3 | `Post by @shotovim on X`（Obsidianおすすめ機能） | @shotovim | `01_AI/02_Claude Code実践/11_Obsidianのおすすめ機能と選ぶ理由` | `01_raw-sources/41_…（出典）` |
| 4 | `どう頼むかがAIの成果を決める…`（発注の型） | sh-fukaya | `01_AI/03_エージェント運用・ガバナンス/03_AI協業の発注の型 HITL実務` | `01_raw-sources/42_…（出典）` |

採番は各フォルダの既存最大番号+1（02_ツール=08/09、02_Claude Code実践=11、03_ガバナンス=03、raw-sources=39〜42）。

## 各ファイルでやること（1クリップにつき4工程）

CLAUDE.md の Ingest 手順に厳密準拠：

1. **出典ページ作成**（`01_raw-sources/`）：frontmatter `title/tags:[raw-source]/source/author/published/created`＋出典メタデータ＋本文（クリップ全文を再構成して転記）。要約ページへの Wikilinkを付ける
2. **要約ページ作成**（`02_wiki/summaries/...`）：既存フォーマット準拠（frontmatter `title/tags/source/author/published/created`＋見出し「一行で／解く問題／核心／本ボルト内の位置付け／関連」）。出典ページへの Wikilink、関連ページ（既存ノート）への相互リンク
3. **Clippings/ のクリップを `99_to_delete/` へ移動**（環境上の削除不可のため移動で代用・CLAUDE.md 規約通り）。※ユーザー確認：処理済みの残り9本は今回は触らない
4. **既存ページの関連欄更新**：必要な箇所に新規ページへの逆リンクを追記

## 全体更新（最後に一括）

5. **`index.md`**：4件を各カテゴリ欄に追記（一行説明付き）。`updated:` 日付を 2026-08-01 に更新
6. **`log.md`**：末尾に `2026-08-01 Ingest（Clippings新規4件一括処理）:` のブロックを追加（既存の2026-08-01 5件ブロックと同じ書式）

## 各要約ページの要点（内容設計）

- **#1 hi_bysir**：Herdr（ターミナルマルチプレクサ+Agent First）＋Termius＋sshでスマホからMac上のcodex/ccを操作。Amphetamineでスリープ防止。公網はFRP。Vibe Kanban/agmsg と並ぶ「エージェント運用の周辺ツール」として位置付け。※コメントでPaseo/網易uu等の代替言及ありと注記
- **#2 ephemeral-sandbox**：並列コーディングエージェント向けOSS基盤（Rustコア・MIT）。共有ベース+ワークスペース分離+原子公開。LayerStack・CLI/MCP 3ツール群。[[05_Vibe Kanban]]（人間側UI）・[[11_Graph of Loops]]（worktree隔離）・[[10_1チャットをエージェントチームへ]]と並ぶ「エージェント並列のインフラ層」
- **#3 shotovim**：Obsidianのおすすめ機能ランキング（Git連携/内部リンク/Web Clipper/Bases/Canvas/プラグイン/デイリーノート）。「ClaudeCodeが〜は理由にならない（ローカルファースト設計の帰結）」の指摘を拾う。本ボルト運用ルールとの対比で肉付け。情報量少なめを注意書き
- **#4 発注の型**：HITL実務。出力側の壁（抽象的要約/一気の出力）＋入力側の壁（頼み方が言語化できない）。人が触るのは依頼・承認・検証の3点。3つの型（結論ファースト+構成承認／決め手ポイント1問ずつ／結論を仮説に決め手だけ検証）。コピペレシピ付き。[[02_24時間自走する…]]（Guardrail層）・[[01_エージェントファクトリ…]]（検証ステーション）と補完

## 最終成果物（計10ファイル作成＋2ファイル更新）

**新規作成（10）**：
- `01_raw-sources/39_スマホからPCのcodexとccをリモート操作（出典）.md`
- `01_raw-sources/40_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤（出典）.md`
- `01_raw-sources/41_Obsidianのおすすめ機能と選ぶ理由（出典）.md`
- `01_raw-sources/42_AI協業の発注の型 HITL実務（出典）.md`
- `02_wiki/summaries/02_ツール/08_スマホからPCのcodexとccをリモート操作.md`
- `02_wiki/summaries/02_ツール/09_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤.md`
- `02_wiki/summaries/01_AI/02_Claude Code実践/11_Obsidianのおすすめ機能と選ぶ理由.md`
- `02_wiki/summaries/01_AI/03_エージェント運用・ガバナンス/03_AI協業の発注の型 HITL実務.md`

**移動（4）**：Clippings/ の4ファイル → `99_to_delete/` へ

**更新（既存ページの関連欄＋インデックス系、必要なもののみ）**：
- `index.md`（4件追記＋updated日付）
- `log.md`（Ingestブロック追記）
- 必要に応じ既存ページ（Vibe Kanban/graphify/Graph of Loops 等）の関連欄に新規ページへの逆リンク

## 注意点

- **Wikilink はファイル名（プレフィックス含む）で張る**（CLAUDE.md 規約）
- **frontmatter `title:` はファイル名と完全一致**させる
- **削除せず `99_to_delete/` へ移動**（環境制約）
- 作業完了後、触った全ファイルを提示する