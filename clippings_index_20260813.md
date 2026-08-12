# Clippings 仕訳インデックス — 2026-08-13

`Clippings/` ディレクトリ（60件）の仕訳状況。

## サマリー

| 区分 | 件数 | 状態 |
|------|------|------|
| 既に出典ノート化済み | 50件 | ✅ `01_raw-sources` に対応ノートあり |
| 今回新規仕訳 | 10件 | ✅ `01_raw-sources` に `96〜105` として作成 |
| **合計** | **60件** | 全件仕訳完了 |

---

## 今回新規作成した出典ノート（96〜105）

| # | 出典ノート | 元クリップ | 出典 | トピック |
|---|-----------|-----------|------|---------|
| 96 | Graph Engineering 11-Step Roadmap Obsidian→Fable5 | `Clippings/Graph Engineering The 11-Step...` | @unicodef1wn | Graph Engineering |
| 97 | AI協業の発注の型 HITL実務 | `2026-08-01/どう頼むかがAIの成果を...` | Qiita sh-fukaya | HITL・発注 |
| 98 | 富士通デザイン思考テキストブック | `2026-08-01/富士通の実践知が...` | note 富士通デザイン | デザイン思考 |
| 99 | intent-cli v0.6.2 クロスプロジェクト分離 | `2026-08-02/Release intent-cli v0.6.2` | GitHub J-Tech-Japan | intent-cli |
| 100 | ループエンジニアリング実践 | `Clippings/Claude Code で「ループ...」` | Zenn てつどん | Loop Engineering |
| 101 | RAG vs Graph RAG vs Agentic RAG | `Clippings/Post by @DailyDoseOfDS_...` | @DailyDoseOfDS_ | RAG |
| 102 | superpowers Agentic Skills Framework | `Clippings/Post by @connect24h on X.md` | @connect24h / obra | Skills |
| 103 | opc-skills ソロプレナー向け | `Clippings/Post by @trendtech33566 on X.md` | @trendtech33566 | Skills |
| 104 | Higgsfield Supercomputer マルチモデル | `Clippings/Stop Switching Between...` | @AnatoliKopadze | マルチモデル |
| 105 | connect24h Playwright E2E責務分離 | `2026-08-01/Post by @connect24h on X.md` | @connect24h | E2Eテスト |

---

## 重複・注意事項

### 1. 同一URL・別内容（3ファイル）

`How to Become a Graph Architect With Zero Experience (Full Course)` が3日付に同一URLで存在:

| パス | md5 | 備考 |
|------|-----|------|
| `2026-08-01/` | `7cb1455a...` | 既出典ノート `44_` の元 |
| `2026-08-05/` | `a3c7edb4...` | 内容に差異（コメント追加分等） |
| `2026-08-08/` | `5520182f...` | 内容に差異 |

→ 同一ポストだがクリップ時期で内容が変化。出典ノート `44_Graph Architectへの20ステップ5フェーズ（出典）` で処理済み。08-05・08-08 は実質重複。

### 2. 同名ファイル・別ポスト（重複ではない）

X の「Post by @xxx」という汎用タイトルのため、同名でも**別URL（別ポスト）**の場合がある:

| アカウント | ファイル数 | URL | 判定 |
|-----------|-----------|-----|------|
| @DailyDoseOfDS_ | 2 | 別URL | ✅ 別ポスト |
| @ai_hakase_ | 2 | 別URL | ✅ 別ポスト |
| @beamnxw | 2 | 別URL | ✅ 別ポスト |
| @connect24h | 2 | 別URL | ✅ 別ポスト |
| @trendtech33566 | 2 | 別URL | ✅ 別ポスト |

### 3. 出典ノート内の注意

- `97_AI協業の発注の型` は既存 `42_AI協業の発注の型 HITL実務（出典）` と内容が近い。要約時は統合を検討
- `42` のソースURLが未確認のため、別出典か同一かは後日確認が必要

---

## 推奨アクション

| アクション | 対象 |
|-----------|------|
| 重複クリップの整理（削除検討） | `2026-08-05/How to Become a Graph Architect...`, `2026-08-08/How to Become a Graph Architect...` |
| 出典ノート `42` と `97` のソースURL照合 | 両者の統合可否判断 |
| Clippings 直下の5ファイル（日付フォルダ外）の日付振り分け | `Clippings/*.md`（直下5件）を適切な日付サブディレクトリへ移動検討 |
