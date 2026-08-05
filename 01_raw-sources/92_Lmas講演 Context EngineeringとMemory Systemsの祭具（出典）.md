---
title: "92_Lmas講演 Context EngineeringとMemory Systemsの祭具（出典）"
tags: [raw-source]
source: AI Dev Day 講演（Anthropic Applied AI チーム）

speaker: Lmas（Anthropic Applied AI チーム・Member of Technical Staff）
created: 2026-08-06
language: 英語音声→日本語要約（主題別再構成）
---

# 出典メタデータ

- 話者: Lmas（Anthropic Applied AI チーム・Member of Technical Staff・startup/founder 向け）
- 形態: 講演の音声書き起こし（AI Dev Day）
- 主題: Context Engineering の1年の進化・memory management の SOTA・production での構築・dreaming（out-of-band memory curation）・continual learning
- 取得: ユーザーが `tc/output/20260806_081343_transcription.txt` として提供

要約は [[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]] を参照。

> [!note] 本文について
> 本出典は音声書き起こしで、一部誤認識（「Ampropic」は「Anthropic」、「reming/reaming」は「dreaming」、「Lamas/Lameck」は「Lmas」、「cloud managed agents」は「Claude managed agents」等）を含む。下記は主題別に再構成した日本語訳。

---

# 講演内容（主題別再構成・日本語）

## 導入 — 知性だけでは複利が効かない

モデルはより知的になるが、**知性だけではagent/環境/組織へのデプロイで複利が効かない**。モデルがタスクを遂行するには context が必要。この context は多くの場合**モデル知性と直交**する（新モデルは箱から出しても組織のことを知らない）。Context engineering への投資は、モデルが賢くなるにつれて**知性を乗算**する効果がある。

## 1. Context Engineering の1年の進化（4段階）

### (1) CLAUDE.md ファイル
- Claude Code ローンチ時。markdown でコードベース案内・組織・ユーザー設定をセッション開始時に注入
- 有効だが、**ファイルが長くなる問題**（context を圧迫）

### (2) Memory tools（自律的なメモリ管理）
- agent が自律的に読み・書き・更新を決定（in-band・セッション内）
- **自律性が非常に有効**。ツールの形について次第に opinionated に

### (3) Skills（progressive disclosure）
- **スキルの前付け（front matter）だけを読む** — 本文は必要時に読み込む
- 「本棚の比喩」: 会話のたびに本棚を見渡し、関連する本のタイトルがあれば引っ張り出す。フランス語で話しかけられたらフランス語辞典を
- **深い詳細を持ちつつ context を過負荷にしない**
- ボトルネック: 人間と agent が共同で「何にスキルが必要か」を決める必要

### (4) File systems as memory（SOTA）
- memory system を**ファイルシステムとしてモデル化**。markdown で埋める
- agent は bash/grep 等の通常ファイルシステムツールで検索（専用ツールでなく）
- progressive disclosure を反映。インデックス化でインテリジェントに検索

### キーラーニング
1. Markdown はメモリの読み書きに最適
2. メモリを大きく育てせるが、**関連するものを素早くインデックスするツールを与える**
3. agent にメモリ書き込みの**自律性**を与える

## 2. Production での問題（理論 → 現実）

上記のきれいなアイデアは production でスケールすると問題が噴出:

- **複数 agent が同時に同じメモリファイルへ書き込み** → 競合
- **1 agent が組織全体の context を書き換え** → 全 agent へ影響（誤りが伝播）
- **人間と agent がメモリで協調** → 何が起きているか追跡困難
- **メモリが陳腐化** — 過去の正しいものが今は不正解・誤記載・悪意ある注入

## 3. Production memory の4原則

### (1) Versioning（バージョン管理）
- 版を保存・追跡・ロールバック可能に
- 「どの context（agent session・transcript）がこの更新の根拠か」を追跡
- who（どの agent・人間）がやったか

### (2) Concurrency（並行性 — hashing）
- 数千 agent が同じ memory system で動く時の解法: **hashing**
- agent が書き込み時: ①hash を取る → ②ドラフト・編集 → ③書き込み前に再 hash → ④一致しなければ書き込めない（間に別の更新があった）
- 一致しない時: メモリを再取得 → 新ドラフト → 再 commit（楽観的並行制御）

### (3) Permissioning（権限）
- 組織全体の知識（慎重に curate）→ agent のスクラッチパッド（個別 working memory）まで階層
- 組織全体の context を1 agent が勝手に更新してはならない
- 自分のスクラッチパッドは書き込み自由

### (4) Portability（移植性）
- memory system は将来にわたり極めて重要。複数プロダクトサーフェス・複数システムからアクセス可能に
- 明確な API でポータブルに設計

### Production 効果
- **精度向上**: 2回目のタスクがより良い（何が間違ったかのメモリがある）
- **速度/コスト向上**: トークン消費減・タスク完了容易
- **人間の容量解放**: agent が自己学習ループをバックグラウンドで回す間、プロダクト開発に集中

## 4. In-band memory の限界 — なぜ dreaming が必要か

In-band memory（セッション内で agent が読み書き）には2つの限界がある:

### (1) focus と resource の競合
- agent は「タスク完了」と「メモリ curate（未来の自分を助ける）」を同時に求められる
- どれだけ未来へ投資するか vs 今のタスク — **最適化問題が困難**

### (2) 可視性の制限
- セッション内の context しか見えない — **cross-session のパターンが見えない**
- 同じ間違いを繰り返しても、毎回新しい context なので気づかない
- 複数環境の複数 agent の失敗を、単一 agent は知らない

## 5. Dreaming（out-of-band memory curation）

> **「学校」の比喩**: 多くの生徒（agent）が課題を出し、教師が採点し、校長が全体をレビューする。**専門の dedicated capacity で学習を助ける人**と**艦隊全体の可視性を持つ人**がいる — これが現実世界で機能する理由。

### Dreaming の構造
- **actual context**（agent が参照する有用情報）
- **memory processes**（agent が自律的に管理・in-band）
- **dreaming**（バッチ・非同期・独自のリソースで稼働し、メモリが有効・最新・学習に寄与するよう保証）

### Dreaming の処理
1. 既存 memory store を取得
2. 期間内の session transcripts（agent ↔ user の往復 + ツールコール等のメタデータ）を取得
3. agent が全 transcript をレビュー → memory store と照合 → **uplift のあるパターンを特定**
4. 新 memory store を出力（既存への変更提案）

### Dreaming が見つけるパターンの例
- **地理の生徒全員が同じ質問にゴミを書く** → カリキュラム丸ごと欠落 → 追加提案
- **数学の試験で全員が radian で答える（degree のはず）** → 電卓の設定 → agent では**ツールコールの設定ミス**を検出
- **艦隊全体のパターン**（全員がダッシュを使いすぎ等）→ 組織全体のアナウンス追加

### Production での設計
- orchestrator が sub agent の艦隊を展開し transcripts を分析
- orchestrator がレビュー → 十分に prevalent なパターンのみ memory store への変更を提案
- 各変更に **transcript の例 + 発生頻度の統計 + 更新の根拠**を付加
- 人間が accept/reject を決定

### 並行する2プロセス
- **Memory（in-band）**: agent がセッション内で自律的に読み書き。次回実行で即座に改善（短い time-to-effect）だが、リソース競合・可視性制限あり
- **Dreaming（out-of-band）**: 専門 capacity と全体可視性で学習を促進。コストがかかるが、効果的な memory store で他のコストが下がる

## 6. まとめ — 「do the simple thing that works」

- 最低限: CLAUDE.md・skills・自律的メモリ管理は agent 性能に**巨大な差**をもたらす
- スケール時: versioning・hashing・permissioning・portability の guardrail を追加
- **コーディング限定でない** — プレゼン作成・スライドスタイル等でも memory は有効
- 真にループを閉じるなら: **dreaming（out-of-band）** でメモリを統合・陳腐化削除・欠落追加・整理

## Q&A（質問から抽出）

### Q1: memory storage の実装提案は?
Anthropic の Claude managed agents の memory dreaming API が、versioning・hashing 等の production 機能を out-of-box で提供。

### Q2: enterprise で dreaming の permission をどうスケール?
Dreaming ジョブは**どの transcript を添付するかを設定可能**。既存の agent permission set と同じ transcript のみ検索するよう構成できる。

### Q3: データベースを第一原理から再発明しているのでは?
**「針に糸を通す」** — agent の自律的行動と harness への programmatic baking の境界を見つける。hashing/versioning は従来のソフトウェア工学の原則に回帰しているが、それは**自律的 agent が有効に相互作用できる形で**。十分な signal が溜まり、決定的に行うべきものは harness に codify する。
