---
title: "12_Lmas講演 Context EngineeringとMemory Systemsの祭具"
tags: [summary, ai, context-engineering, memory, dreaming, continual-learning, production]
speaker: Lmas（Anthropic Applied AI チーム・Member of Technical Staff）
created: 2026-08-06
---

# Lmas 講演 — Context Engineering と Memory Systems の生産性

> **Context engineering への投資はモデル知性を乗算する。1年の進化: CLAUDE.md → memory tools → skills（progressive disclosure）→ file systems as memory。production では4原則（versioning・concurrency[hashing]・permissioning・portability）。in-band memory の限界（focus競合・可視性制限）を超えるため、out-of-band の dreaming が cross-session パターンを発見し memory store を更新。**

出典: [[92_Lmas講演 Context EngineeringとMemory Systemsの祭具（出典）]]（Lmas / Anthropic Applied AI。本文はユーザー提供トランスクリプト tc/output/20260806_081343 から主題別に再構成）

---

## 一行で

Anthropic Applied AI の Lmas による、context engineering の1年の進化と production memory systems の実践。in-band memory（セッション内自律管理）の限界を指摘し、out-of-band の「dreaming」で cross-session パターンを発見して memory store を継続的に改善する枠組みを提示。

## 核心 — 知性は context で乗算される

モデルはより知的になるが、**知性だけでは agent デプロイで複利が効かない**。context はモデル知性と直交（新モデルは組織を知らない）。context engineering への投資は**モデルが賢くなるにつれて知性を乗算**する。

## 1. Context Engineering の1年の進化（4段階）

| 段階 | 内容 | 効果・課題 |
|---|---|---|
| **(1) CLAUDE.md** | markdown でコードベース案内・組織・設定をセッション開始時に注入 | 有効だが**ファイルが長くなると context 圧迫** |
| **(2) Memory tools** | agent が自律的に読み・書き・更新（in-band） | **自律性が非常に有効** |
| **(3) Skills** | **front matter だけ読む**・本文は必要時（progressive disclosure） | 深い詳細と context 保護を両立。本棚の比喩 |
| **(4) File systems as memory（SOTA）** | markdown で埋め・bash/grep で検索（専用ツールでなく） | インデックス化でインテリジェント検索 |

### キーラーニング
1. Markdown は読み書きに最適
2. 大きく育てせるが**関連するものを素早くインデックスするツールを与える**
3. 書き込みの**自律性**を与える

## 2. Production memory の4原則

| 原則 | 内容 |
|---|---|
| **Versioning** | 版の保存・追跡・ロールバック。「どの context/transcript が根拠か」「who」を追跡 |
| **Concurrency（hashing）** | ①hash → ②draft → ③再 hash → ④不一致なら書き込めない。楽観的並行制御 |
| **Permissioning** | 組織全体の知識（慎重 curate）から agent 個別のスクラッチパッドまで階層。組織全体を1 agent が勝手に更新させない |
| **Portability** | 複数プロダクトサーフェス・システムからアクセス可能。明確な API |

### Production での問題
- 複数 agent の同時書き込み競合
- 1 agent の組織全体 context 書き換え（誤りが全 agent へ伝播）
- 人間と agent の協調で追跡困難
- メモリの陳腐化・誤記載・悪意ある注入

### Production 効果
- **精度向上**（2回目のタスクが改善）
- **速度/コスト向上**（トークン減・完了容易）
- **人間の容量解放**（agent が自己学習ループをバックグラウンドで回す）

## 3. In-band memory の限界 — dreaming が必要な理由

| 限界 | 詳細 |
|---|---|
| **focus と resource の競合** | タスク完了 vs メモリ curate（未来への投資）の最適化が困難 |
| **可視性の制限** | セッション内しか見えない。cross-session パターン不可視。同じ間違いを繰り返しても毎回新 context で気づかない。複数環境の他 agent の失敗を知らない |

## 4. Dreaming（out-of-band memory curation）

> **「学校」の比喩**: 生徒（agent）が課題提出・教師が採点・校長が全体レビュー。**専門の dedicated capacity** と **艦隊全体の可視性**を持つ層が、現実世界で学習を機能させる。

### Dreaming の3層構造
- **actual context**（agent が参照する情報）
- **memory processes**（agent が in-band で自律管理）
- **dreaming**（バッチ・非同期・独自リソースで稼働し、メモリが有効・最新・学習に寄与するよう保証）

### Dreaming の処理フロー
1. 既存 memory store を取得
2. 期間内の transcripts（往復 + ツールコール等メタデータ）を取得
3. agent が全 transcript をレビュー → memory store と照合 → **uplift パターンを特定**
4. 新 memory store を出力（変更提案 + transcript 例 + 発生頻度統計 + 根拠）
5. 人間が accept/reject

### Dreaming が見つけるパターンの例
- 全員が同じ質問にゴミ回答 → カリキュラム（memory）丸ごと欠落 → 追加
- 全員が radian で答える（degree のはず）→ **ツールコールの設定ミス**を検出
- 艦隊全体のパターン（ダッシュ使いすぎ等）→ 組織全体のアナウンス

### 並行する2プロセス
| | Memory（in-band） | Dreaming（out-of-band） |
|---|---|---|
| time-to-effect | 短い（次回セッションで即改善） | 遅い（バッチ） |
| リソース | タスクと競合 | **専門 capacity** |
| 可視性 | セッション内のみ | **艦隊全体** |
| コスト | 低い | かかるが、効果的 memory で他のコストが下がる |

## Q&A の核心

- **「データベースを再発明しているのでは?」** → **「針に糸を通す」** — agent の自律的行動と harness への programmatic baking の境界を見つける。十分な signal が溜まったもの（hashing/versioning）は harness に codify する（決定的に）。これは本ボルトの CLAUDE.md・3層・Lint 運用と同じ方向。

---

## 本ボルト内の位置付け

- **「知性は context で乗算」** は [[10_Memory Engineering 最も見過ごされる層]]「Context engineering は今見るもの・Memory engineering は来週知っているもの」の Anthropic 公式版。両者は表裏
- **4段階の進化（CLAUDE.md → tools → skills → file systems）** は本ボルト運用の中核と完全に一致:
  - CLAUDE.md（本ボルトの CLAUDE.md・3層・Ingest/Query/Lint）
  - skills（progressive disclosure・本ボルトの index.md が working memory・Wikilink が検索パス）
  - **file systems as memory** = 本ボルトの `01_raw-sources` + `02_wiki` + `index.md` + `log.md` がまさにこれ。bash/grep で検索（専用ツールでない）という方針も一致
- **production 4原則（versioning/hashing/permissioning/portability）** は本ボルトの Lint 操作（矛盾検出・孤立ページ・陳腐化検出）を production grade へ引き上げる指針。本ボルトは現在 git（versioning）のみで、hashing（並行制御）・permissioning（書き込み権限の階層）・portability（API）は未導入
- **dreaming（out-of-band）** は本ボルトの Lint 操作（週1・手動）の**自動化・バッチ化版**。[[18_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]]（nightly compiler・日曜 health pass）・[[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]（Claude Code 自身を自律保守するルーチン）と同じ「スケジュールドエージェントでメモリを保守」系譜
- **「学校の比喩（生徒・教師・校長）」** は ccc（[[03_ccc関連事例調査 ボルト内の同じアプローチ]]）の5ロール（采/計/作/査/析）と同じ階層的協調。dreaming の orchestrator → sub agent 艦隊は ccc の 采→計→作 に相似
- **「針に糸を通す・harness へ codify」** は [[11_Thoric講演 Fableフィールドガイド unhobblingとunknowns]]（「モデルは育つ・私たちの理解が束縛」）・[[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]（システムプロンプト80%削除・simple mode）と同根。**自律性と決定的制御の境界**をモデル進化に合わせて動かす
- **「ツールコールのメタデータまで審査」** は [[05_Claude Codeの6層アーキテクチャ ダムループ]]（観測可能性層・イベントバスが全ツールコールをログ）・ccc の査（実機検証）と同根。transcript の表面でなく構造を見る

## 本ボルト運用への示唆

本ボルトの Lint 操作（手動・週1）は、Lmas の言う「in-band memory の限界（focus競合・可視性制限）」と同じ状況にある。**dreaming（out-of-band・バッチ）への移行**が次段階:
1. **transcripts（log.md の全エントリ）を定期的にレビュー**し、cross-session パターン（繰り返し現れる誤り・陳腐化した記述）を発見
2. memory store（02_wiki）への変更提案を人間が accept/reject
3. [[18_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]] の nightly compiler と統合

---

## 関連

- Memory Engineering（表裏の概念） → [[10_Memory Engineering 最も見過ごされる層]]
- nightly compiler（メモリの自動保守） → [[18_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]]
- Claude Code 自身を自律保守 → [[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]
- unhobbling（自律性と harness の境界） → [[11_Thoric講演 Fableフィールドガイド unhobblingとunknowns]]
- 観測可能性（ツールコールまで審査） → [[05_Claude Codeの6層アーキテクチャ ダムループ]]
- ccc の5ロール（階層的協調） → [[03_ccc関連事例調査 ボルト内の同じアプローチ]]
- 第二の脳の4層×1writer → [[05_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]]
- 短期/長期記憶・LangGraph checkpoint → [[08_LangGraph Academy エージェント構築のコース]]
- 知識グラフで検索品質 → [[04_オントロジーでClaude性能向上 知識グラフの実測]]
