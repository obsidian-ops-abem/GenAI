---
title: "07_Everything Fable 5 Mythosクラスとプロンプトガイド"
tags: [summary, ai, llm, model, fable5, prompting]
source: https://x.com/mikenevermiss/status/2073278341377912944
author: Mike (@mikenevermiss)
published: 2026-07-04
created: 2026-08-02
---

# Everything Fable 5 — Mythosクラスとプロンプトガイド

> **Fable 5 は Opus の上に位置する「Mythos-class」。長期・多段階・曖昧な仕事で真価を発揮し、effort（low/medium/high/xhigh）というダイヤルと classifier（安全性再ルーティング）が新しい制御面。8つのプロンプトと5つの誤用パターンを実用ガイドにまとめた記事。**

出典: [[56_Everything Fable 5（出典）]]（Mike @mikenevermiss, 2026-07-04。本文はクリップ全文を使用。価格・アクセス条件は 2026-07-04 時点）

---

## 一行で

Anthropic の最上位モデル Claude Fable 5（Mythos-class）について、能力特性・安全性システム（classifier）・effort レベル・8つの即プロンプト・5つの誤用・スキップすべきケースを網羅した実用ガイド。

## Fable 5 とは（Mythos-class）

- **Opus の上**に位置する新階層「Mythos-class」— 「週単位の実仕事を自律完了できる能力」を要するため、一般公開前に新たな安全策が必要な階層
- 2026-06-09 ローンチ。**Mythos 5** は同じモデルで安全制限を減らした版（Project Glasswing の審査パートナー限定）。**Fable 5** は classifier（要監視 AI）を内蔵した一般向け版
- 2026-06-12〜07-01 は第三者 jailbreak 報告に伴う米国輸出管理命令で一時停止 → 07-01 グローバル再開

## 何が得意か

- **長く多段階な仕事**が最大の性能向上箇所（quick one-offs でなく）
- **Stripe**: 5000万行 Ruby コードベースの完全移行を1日で完了（手動なら2ヶ月超）。plan → execute → check own work → 長期にわたり維持 できる証拠
- 知識作業: 文書多用分析・表/図解釈・多ソース研究。Hebbia Finance Benchmark（シニア級推論）で最高、IMC トレード分析でほぼ全域合格
- ビジョン: SOTA。スクショだけで Web アプリのソース再構築・密な科学図からの精密抽出・ぼやけ/反転画像も対応
- **1M トークンコンテキストウィンドウ**（デフォルト）＋ タスク途中で自分にメモを書き出力を改善
- **前世代より最大の向上は「難しく長く曖昧な問題」**。単純タストでテストすると能力を過小評価する（公式注意）

## 安全システム: classifier（再ルーティング）

- 各リクエストを監視する並列 AI。フラグされると **Opus 4.8 へ自動再ルーティング**（Fable 料金でなく、回答は返る）
- 3つの発火ドメイン: **サイバーセキュリティ**（脆弱性発見/悪用）/ **生物・化学**（実験手法・分子機構・遺伝）/ **蒸留**（モデル能力の大規模抽出）
- 意図的に広く設定 → benign なリクエストも捕まる（平均 <5%、生化学は更に広い）
- 再ルーティング対策: ①なぜ聞くかの文脈を追加 ②benign な目的を具体的に再表現（classifier は意図シグナルを見る）
- **確実な発火要因**: 「show your thinking」「narrate your reasoning」等の可視的推論説明要求 → 推論抽出 classifier を引き Opus フォールバック。推論を見たいなら Claude Code の structured thinking output を使う

## effort レベル（ダイヤル）

| レベル | 特徴 | 用途 |
|---|---|---|
| low / medium | 高速・安価・依然有能力。medium の Fable は前世代の max より勝る | 定型・要約・即ドラフト |
| **high** | **実仕事の推奨デフォルト**。より多く文脈収集・自己検証・厳密。plan before acting / verify before reporting | 通常の実仕事 |
| xhigh | 最も難しく能力感度の高いタスク。熟考・長実行・徹底検証。「最高 effort で自身の仕事を振り返り検証する。それが高度な自律運用を可能にする」（Knotting 初期テスター）。遅く高価 | 最高難度タスクのみ |

Claude Code では effort を直接設定可能。claude.ai にはトグルがないが**自然言語で同等効果**:「quick pass on this / fast first draft」で低、「this is important, I want your most thorough work / take your time, check your reasoning carefully」で高。モデルは明示的意図フレームに応答。

## 8つのプロンプト（共通パターン）

全プロンプトが従うパターン: **目標と成功基準を述べる + 理由を与える + いつ止まるか/続けるかを指示する**

1. **長文書分析** — dense report/contract/paper。全文要約でなくセクション毎に重要点を旗、検証要に旗
2. **多ソース研究ブリーフ** — 複数入力の統合。同意点/ conflict / 未回答を構造化、別々要約でなく全て横断統合
3. **長文コンテンツドラフト** — 完全初稿（アウトラインでなく）。自分の文章を貼付け音声一致を要求
4. **現実的制約付きプロジェクト計画** — 理想でなく現実的フェーズ計画。スケジュールきつい点・遅延依存・削るべきを旗
5. **スプレッドシート/データ分析** — 計算でなく解釈。データ品質問題→目標関連上位2-3発見→単独で結論すべきでない点
6. **拡張自律タスク (Claude Code)** — リアルタイム監視しない多段階。破壊的/不可逆/スコープ変更/自分しか提供できないもの以外は end to end 進行。各主張を実結果で検証。失敗は率直に。「計画や未実施 next steps でターンを終えない。仕事を終える」
7. **フィードバック・編集** — 全面書換でなく外科的編集。各問題を引用→原因→修正。効いているものは触らない
8. **意思決定分析** — 合意でなく構造化推論。各選択の最強論拠・過小評価リスク・決定を変える追加情報。「聞きたいことを言うでなく、情報不足ならそう言う」。最後に1明確推奨+検証すべき1点

## 5つのよくある誤用

1. **小タスクに使う** — 長く複雑な仕事向け。短いメール要約は「貨物エレベーターで1袋」Sonnet 5 / Haiku 4.5 が速安
2. **即応を期待** — high/xhigh で数分。計画・実行・自己検証中。30秒で閉じると不十分
3. **classifier 拒絶を最終回答と扱う** — 注意深いだけで永続拒絶でない。文脈追加・再表現・benign 目的の再記述
4. **止まる時を指示しない** — 明示的停止条件がないと進み続ける（要求外の follow-up・過剰 refactor・水増し）。「done の姿」を伝える
5. **応答内で内部推論の説明を求める** — 推論抽出 classifier を引き Opus フォールバック。structured thinking output を使う

## スキップすべき人

- 即答/1文出力/速い創作 → **Sonnet 5**（速く顕著に安い）
- 高ボリューム定型を厳予算で → **Haiku 4.5**（速度設計優先）
- リアルタイム/高ボリュームパイプライン（レイテンシ重視）→ Sonnet 5 / Haiku 4.5
- サイバーセキュリティ/生物/化学が中心 → classifier 再ルーティングが定常摩擦。**Opus 4.8 が日中ドライバとして安定**

---

## 本ボルト内の位置付け

- **モデル選定の実用基準**を与える。本ボルトは Claude Code で運用されるため、いつ Fable/Sonnet/Haiku/Opus を選ぶかの判断材料。特に「単純タスクでテストすると能力を過小評価」「長く曖昧な問題で真価」は本ボルトの Ingest/Query/Lint 各操作のモデル割当てと関連
- **effort = ダイヤル**と **classifier 再ルーティング**は、ハーネスの Execution Control（model selection / approval gates）の一要素 → [[04_Agent Harness vs Loop vs Graph Engineering]] の Harness 6要素と整合
- **「止まる時を指示」「推論を見るなら structured thinking output」** は Loop の stop rule（証拠で止まる）・観測性と通底 → [[04_Agent Harness vs Loop vs Graph Engineering]]・[[03_CLINEに全部賭けろ コーディングエージェント時代のプログラマ]]（テストで仕様表現）
- **1M コンテキストウィンドウ**が「全ボルトを1リクエストに収める」可能性を開く → [[05_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]]（1M が retrieval pipeline を不要化）と直結

---

## 関連

- ハーネスの model selection / approval gates → [[04_Agent Harness vs Loop vs Graph Engineering]]
- テストで仕様表現・証拠で止まる → [[03_CLINEに全部賭けろ コーディングエージェント時代のプログラマ]]
- 1M コンテキストが第二の脳を変える → [[05_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]]
- モデル探索のコツ（Hugging Face） → [[06_Hugging Faceでモデルを探すコツ]]
