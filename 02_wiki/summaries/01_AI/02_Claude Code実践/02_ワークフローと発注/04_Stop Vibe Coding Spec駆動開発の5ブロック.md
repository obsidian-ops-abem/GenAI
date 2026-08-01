---
title: "04_Stop Vibe Coding Spec駆動開発の5ブロック"
tags: [summary, ai, claude-code, spec-driven, workflow]
source: https://x.com/0xJeyx/status/2082452097740505509
author: Jey (@0xJeyx)
published: 2026-07-29
created: 2026-08-02
---

# Stop Vibe Coding — Spec駆動開発の5ブロック

> **AI の出力を予測可能にするのは「より良いプロンプト」でなく「1つの文書（spec）」。spec は実装でなくシステムを記述する実行計画。5ブロック（Why/What/Constraints/Out of scope/Tasks）+ 5ステップループ。業界4巨頭（Amazon Kiro / GitHub Spec Kit / Google / Microsoft）が考え記事でなく道具を出荷した。**

出典: [[59_Stop Vibe Coding spec-driven development（出典）]]（Jey @0xJeyx, 2026-07-29。本文はクリップ全文を使用）

---

## 一行で

Vibe coding（プロンプトを行き当たりばったり修正）の失敗の本質は「コードが悪い」でなく「決定が自分のものではなかった」こと。spec-driven development は spec という実行計画書で曖昧さを先取りし、承認済みの1経路を spec→code で走らせる手法を説く。

## なぜ vibe coding が壊れるか

- 「add authentication」は具体的に聞こえて**実はそうでない**: トークン戦略 / セッション長 / refresh / パスワードリセット / ソーシャルログイン / DB / ORM / エラー形 / トークン保存場所 / テスト要否…
- これらを全て答えない → エージェントが**静かに代わりに決定**。実行後にどの答えを得たか知る
- **本当の失敗**: コードは概ね問題ない。決定が自分のものではなかった
- 同じプロンプトを100回走らせれば100通りのビルド。どれも間違いでないが、頭にあったものでもない

## 3つの文書の混同（語彙の整理）

| 文書 | 読者 | 答える問い | 中身 |
|---|---|---|---|
| **PRD** | 人（PM・ステークホルダー・承認者） | what & why | ビジネス価値 |
| **design doc** | エンジニア | how | アーキテクチャ・スケーリング・セキュリティ・トレードオフ |
| **spec** | **エージェント**（新） | 実行されるための計画 | PRD から why を少々借りるが、議論でなく実行目的 |

- spec を PRD のように書く → エージェントが行動不能なビジネス正当化に溺れる
- design doc のように書く → 受け入れ基準のないアーキテクチャになる
- **spec は独自の文書・独自の仕事**

## spec の5ブロック（核心）

1. **Why** — 2-3文の文脈。何の問題を・誰のために。**エージェントが指定忘れに直面した際の合理的判断を可能にするブロック**
2. **What** — 観測可能な振る舞い。エンドポイント・入力・出力・状態。2人のエンジニアが読んで同じものを構築できる程度に具体
3. **Constraints** — **最も過小評価**。spec を「願い」から切り離す。コーディングエージェントは熱心で、放置するとパッケージ導入・パターン発明・触れていないファイルの refactor をする。**事前に no と言う場所**
4. **Out of scope** — 別リスト（意図的）。Constraints は「どう作るか」、Out of scope は「何に触れないか」。全機能にはエージェントが親切に追加する隣接機能がある。排除しないと追加される
5. **Tasks** — 離散作品に分割。各々「何を・どのファイル・完了をどう知るか」

> spec は主要成果物。実装・テスト・docs 全てがそこに遡る

## 完成した spec の例（認証）

```
WHY: ユーザー共有アカウント → 個別アカウント（課金追跡・設定）
WHAT: POST /auth/register,/login,/refresh ・ JWT(1h)+refresh(7d) ・ {error,code}
CONSTRAINTS: Postgres+Prisma ・ 新依存関係追加禁止 ・ アクセストークンDB保存禁止 ・ 既存ライブラリでPWハッシュ
OUT OF SCOPE: パスワードリセット・ソーシャルログイン・Email認証・レート制限
TASKS: 1.User model+migration(done=fresh DBでclean) 2.Register(done=重複email→409) 3.Login(done=有効資格→200+token) 4.Refresh(done=期限切れ→clean refresh)
```

> Constraints の4行が**エージェントが代わりに決めるはずだった決定を全て閉じる**。最もスキップされ最も仕事をするブロック。Tasks も各々ファイル＋完了条件。「auth を構築」でなく、構築対象・場所・完了テスト

## spec → コードの5ステップループ

1. **Generate** — 機能を記述しエージェントに spec ドラフト作成。手書き初版は遅くても良くない
2. **Review** — 敵対的に読む。エージェントが推測できる余地を今ここで閉じる。残した曖昧さ = 後の書き直し
3. **Break down** — 一座で完了・検証できる大きさのタスクへ分割
4. **Run one task** — **1つだけ**。spec 全体でなく。最も間違えられるステップ
5. **Review and commit** — 既に書いた完了条件と照合 → commit → 次タスク

> 新エンジニアに40ページ機能文書を渡して去ることはない。1チケット渡し・レビュー・次を渡し。エージェントも同じ。スキップすると同じように失敗する
>
> **複利の利益は第2セッションで現れる**: spec が残り、タスク4をタスク1と同じ制約で継続。drift しない。「賢いプロンプト」からの利益はとっくに止まった。今は**より厳しい spec** から来る

## spec が無駄な時（正直なセクション）

spec は予測可能性を買う。速さを買わない。小仕事では両方を失う。スキップ条件:

- 1ファイル1関数のタスク（spec 書きが修正より長い）
- まだ探索中（何が欲しいか不明なら、spec は推測を固定し放棄を高価にする）
- 使い捨て（金曜に消すプロトタイプに契約不要）
- どの手法でも気にしない（合理的実装なら何でも良ければ任せる）

**判定**: エージェントが特定の決定を違えても苛立つか? Yes → spec 行き。肩をすくめる → vibe してよい

**逆の罠**: spec が実装そのものになるほど詳細なら、それは散文で書かれたコード。2度保守することになる。**spec が機能より長ければ、spec でなく the thing を書いた**

## 産業の裏付け（考えでなく道具）

Amazon（Kiro: spec 重心 IDE）/ GitHub（Spec Kit: OSS）/ Google（Antigravity の spec-driven codelab）/ Microsoft（開発ブログ連載）。**地上最大4組織が考え記事でなく道具を出荷した**

## 核心的転倒

> For years the spec lived in your head and the code was the thing you shipped. Spec-driven development swaps them.

spec = 書き・承認するもの。code = エージェントがそこから構築するもの。仕事は「タイプする人」から**「決定する人」**（何を・何でなく・境界をどこに）へ。how はエージェント。

> Vibe the demo. Spec the thing you actually ship.（デモは vibe で。実際に出荷するものは spec で）

---

## 本ボルト内の位置付け

- **「決定を自分のものに」「曖昧さを先取り」** は本ボルト運用ルール（CLAUDE.md・3層・Ingest/Query/Lint）と同型。raw-sources に「読むだけ・書き換えない」を固定し、wiki の生成規則を frontmatter・命名・Wikilink で縛る = spec で境界を決めること
- **Constraints + Out of scope** は [[04_Agent Harness vs Loop vs Graph Engineering]] の Harness「Safety And Governance（least-privilege / isolation / allowlists）」・Harness の「怠ると permission too much」と同じ。エージェントの熱心さに事前に no を言う
- **5ステップループ・Run one task** は [[02_Claude Code 計画と実行を分けるワークフロー]]（計画と実行の分離）・[[03_AI協業の発注の型 HITL実務]]（承認ポイントを絞る・1問ずつ）と同構造。[[03_CLINEに全部賭けろ コーディングエージェント時代のプログラマ]]（得意領域を1つずつ・テストで完了判定）と通底
- **「賢いプロンプトでなく厳しい spec」** は [[03_CLINEに全部賭けろ コーディングエージェント時代のプログラマ]]（`.clinerules`/CLAUDE.md に大量指示・汎用プロンプトの限界）・[[06_Context Engineering Claude Codeの文脈設計]]（発話前の約7,850トークンを設計）の主張と一致
- **「spec が実装になったら2度保守」** は [[02_Graph Engineering 最大の間違い Loop↔Graph判断]]（過剰設計警告）・[[04_Graph Architectへの20ステップ5フェーズ]]（Phase5「グラフにしない」）の慎重姿勢と同系。過剰 spec = 過剰グラフ
- **「タスクは完了条件付き」** は Loop の stop rule「証拠で止まる」（[[04_Agent Harness vs Loop vs Graph Engineering]]）・checker node（[[05_Graph Engineering 入門 What It Is]]）と同根
- spec = ドキュメント先行は本ボルトの「raw-sources を先に置いてから wiki を生成」と同じ順序

---

## 関連

- 計画と実行の分離・1タスクずつ → [[02_Claude Code 計画と実行を分けるワークフロー]]
- HITL承認ポイントを絞る → [[03_AI協業の発注の型 HITL実務]]
- CLAUDE.md/.clinerules で文脈を厳密化 → [[03_CLINEに全部賭けろ コーディングエージェント時代のプログラマ]]
- Context Engineering（発話前設計） → [[06_Context Engineering Claude Codeの文脈設計]]
- Harness の Safety/Governance（事前に no） → [[04_Agent Harness vs Loop vs Graph Engineering]]
- 過剰設計警告（過剰 spec = 過剰グラフ） → [[02_Graph Engineering 最大の間違い Loop↔Graph判断]]
