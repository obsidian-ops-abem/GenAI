---
title: "07_Harness Engineering 壊れないAIエージェントの作り方"
tags: [summary, ai, agent-design, harness, contract, durable-state, permissions, traces]
source: https://x.com/0xwhrrari/status/2093685107534000560
author: whrrari（@0xwhrrari）
published: 2026-08-29
created: 2026-09-29
---

# Harness Engineering — 壊れない AI エージェントの作り方

> **失敗するエージェントへの対応で多くはプロンプト→モデル→コンテキスト窓の順に変える。だが問題は知性でなく環境（ハーネス）。プロダクション・ハーネスの7仕事（契約/地図/ツール/永続state/センサー/モデル外権限/トレース）・「指示をインフラに二重化」・「失敗はクラスを修理」・脳/手/履歴の分離・変更レシート・レベル0-3の最小出発・チェックリスト12項。「より強いモデルはシステムを信頼可能にしない。失敗を高くするだけ」。**

出典: [[116_Harness Engineering 壊れないAIエージェントの作り方（出典）]]（@0xwhrrari, 2026-08-29。本文はクリップ全文を使用。Dario Amodei・OpenAI Codex・Anthropic の公式投稿を引用）

---

## 一行で

@0xwhrrari（[[01_LOOP vs GRAPH vs HARNESS ENGINEERING]] と同一著者）による Harness Engineering の完全ガイド。モデルは推論エンジンに過ぎず、信頼性は環境（ハーネス）が決める、として7仕事とチェックリストを提示。

## 核心 — プロンプト改良 vs ハーネス改良

> **Prompt engineering improves the instruction. Harness engineering improves the conditions under which the instruction is executed.**

- 同じモデルでも、チャットボックスに入れれば質問に答えるだけ。リポジトリ+ターミナル+テスト+ブラウザ+隔離worktree+レビューループに入れればソフトウェアを ship する。**重みは変わっていない。ハーネスが変わった**
- OpenAI Codex の初期停滞は「環境が仕様不足だった」ことでありモデルの能力不足でなかった

## プロダクション・ハーネスの7仕事

| # | 仕事 | 要点 |
|---|---|---|
| 1 | **リクエストを契約に** | goal/inputs/output/constraints/**done_when**。「**黙ってタスクが再定義される**」のを防ぐ。ないと別の仕事を完了しても成功宣言できる |
| 2 | **地図を与える** | 小さなルートガイド（AGENTS.md）→ architecture/testing/product/security/task別。**地図はcontextを保存し、巨大マニュアルは消費する**。詳細は統治対象の近くに置き必要時のみ読む |
| 3 | **正しい環境に正しいツール** | 明確な目的/予測可能な出力/**明示的失敗状態**/権限境界。「**良いツールはモデルが悪く推論する前に曖昧さを減らす。悪いツールは起きたことを当てさせる**」 |
| 4 | **記憶を永続stateへ外置き** | 会話は記録系でない。決定/成果物/失敗/未解決リスクを窗外へ。「**次セッションは会話の劣化復元でなく仕事のstateを継承すべき**」 |
| 5 | **自律の前にセンサー** | テスト/linter/スクショ/ログ/スキーマ検証が**曖昧な品質を証拠に変える**。モデル=成果物を作り、環境=証拠を生み、ハーネス=継続十分か判断 |
| 6 | **権限はモデル外で強制** | MODEL SUGGESTS → POLICY CHECKS → TOOL EXECUTES。「**同じ確率系統に計画の発明とリスクの承認と副作用の実行をさせるな**」 |
| 7 | **トレース記録と局所回復** | 全実行に可読な痕跡。「**トレースなき失敗は謎。トレースありの失敗は次のハーネス改善の入力**」 |

## 鍵となる原則

- **指示をインフラに**: 重要ルールを2重化 — GUIDE（理由を説明）+ CHECK（境界を機械強制・lint等）。「**次のエージェントは事故を覚える必要がない。ハーネスが代わりに覚えている**」
- **ループはハーネスの所有物**: モデルは局所ギャップの修復方法を決め、**ハーネスが再試行の可否を決める**（リトライ上限→人間レビュー）
- **失敗はシステムをアップグレードする**: 多くは現在の出力を修理するが、ハーネスエンジニアは**失敗のクラスを修理する**（MISSING CONTEXT→地図追加 等7対応表）。「**良いハーネスはエージェントの過ちをインフラに変換する**」— 複利優位
- **脳/手/履歴の分離**: BRAIN（推論モデル）/HANDS（サンドボックス+ツール）/HISTORY（追記専用記録）。サンドボックスが死んでも履歴は生きる。**推論エンジンがファイルシステム・権限系・メモリDB・監査ログを兼ねるべきでない**
- **変更レシート**: 毎実行に context_sources/policy_version/model_route/tests/human_corrections/retries/cost/rollback_point を残す。**モデル更新を比較可能に・回帰を帰属可能に・監査を可能に**
- **最小ハーネスから**: LEVEL 0（prompt+model）→1（ガイド+ツール）→2（構造化state+テスト+有界ループ）→3（権限+トレース+回復+人間ゲート）。**タスクが複雑さを稼いだ時だけ上げる。ハーネスは制御する失敗面より小さくあれ**

## チェックリスト12項（抜粋）

成功は実行前に定義されているか／全部を読まずにプロジェクト知識を見つけられるか／各ツールに契約と失敗状態があるか／実行は本番系から隔離されているか／重要決定は会話の外に保存されているか／不可逆は承認で保護されているか／各ループに上限と予算があるか／中断から再開できるか／失敗はガイド/テスト/ツール/ポリシーを更新するか／成果物をロールバックできるか — **複数 No なら、より強いモデルはシステムを信頼可能にしない。失敗を高くするだけ**

## 5層の位置づけ

```
PROMPT -> instruction / CONTEXT -> working view / HARNESS -> operating system
LOOP -> local improvement / GRAPH -> coordination
```
**モデルは来月変わる。ツール・テスト・state・ポリシー・トレースは改善し続けられる。恒久的優位はプロンプトからその周りのシステムへ移動している**

---

## 本ボルト内の位置付け

- **本ボルト運用そのものの設計図**: 7仕事は本ボルトの構造とほぼ1:1 — 契約=CLAUDE.md の Ingest 手順（done_when 相当）/ 地図=index.md（巨大マニュアルでなく地図）/ 永続state=01_raw-sources+02_wiki+log.md（会話でなくファイルが記録系）/ センサー=Lint 操作/ トレース=log.md。**「次セッションが会話の劣化復元でなくstateを継承」はボルト3層の存在根拠そのもの**
- **Harness 6要素の拡張版**: [[04_Agent Harness vs Loop vs Graph Engineering]]（Context Injection/Action Surfaces/Persistence/Execution Control/Safety/Observability）を「7仕事+原則4つ+チェックリスト」に実務展開した最新版。同著者の [[01_LOOP vs GRAPH vs HARNESS ENGINEERING]] の後続
- **「指示をインフラに二重化（GUIDE+CHECK）」** は [[04_Claudeはorchestrator専念 hook強制の分業]]（hook で構造的強制）・[[06_自己改善型AIトレード機械 6段階ループと未解決の6番目]]（kill switch はコード）・ccc の[[03_ccc関連事例調査 ボルト内の同じアプローチ]]（権限はモデル外）と完全に同系の最も明快な定式化
- **「失敗はクラスを修理・過ちをインフラに変換」** は [[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]「コードを直すのでなくプロセスを直す」・[[03_Jev Engineering実践ガイド 分割と失敗モード]] と同じ系譜のハーネス版。本ボルトの Lint（矛盾・陳腐化の洗い出し）も「クラス修理」に相当
- **「同じ確率系統に計画・承認・実行をさせるな」** は [[02_1チャットをエージェントチームへ Opus5 12ステップ]]（検証器は別役割）・[[05_マージゲート4シグナル 信頼スコアの罠とレーン分離]]（エージェントが操作できない決定論的チェック）・ccc の査と同根
- **脳/手/履歴の分離** は [[05_Claude Codeの6層アーキテクチャ ダムループ]]（知性はループ外の層）・[[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]（production 4原則の versioning/portability）・[[10_Memory Engineering 最も見過ごされる層]]（write policy）と直結。**Anthropic Managed Agents の session/harness/sandbox 分離を踏まえる**
- **PROMPT/CONTEXT/HARNESS/LOOP/GRAPH の5層** は既存の [[02_Prompt to Graph Engineering 5層の統一モデル]]・[[06_5層モデル各層の作業単位 プロンプトからグラフへ]] と同じ系譜の @0xwhrrari 版
- **「強いモデルは失敗を高くするだけ」** は [[05_マージゲート4シグナル 信頼スコアの罠とレーン分離]]（賭けでレーン分離）・[[02_24時間自走する自律型AIエージェントの設計図]]（権限4段階）の冷却材的な結論

---

## 関連

- 同一著者の3層診断フレーム → [[01_LOOP vs GRAPH vs HARNESS ENGINEERING]]
- Harness 6要素（LunarResearcher 版） → [[04_Agent Harness vs Loop vs Graph Engineering]]
- 5層モデル（統一版） → [[02_Prompt to Graph Engineering 5層の統一モデル]]・[[06_5層モデル各層の作業単位 プロンプトからグラフへ]]
- hook による構造的強制（GUIDE+CHECK） → [[04_Claudeはorchestrator専念 hook強制の分業]]
- 検証器の独立（承認をモデルにさせるな） → [[02_1チャットをエージェントチームへ Opus5 12ステップ]]
- 決定論的チェック → [[05_マージゲート4シグナル 信頼スコアの罠とレーン分離]]
- プロセスを直す（失敗のクラス修理） → [[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]
- 永続state=記録系（本ボルト3層の根拠） → [[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]
- write policy（会話は記録系でない） → [[10_Memory Engineering 最も見過ごされる層]]
- ccc の権限設計 → [[03_ccc関連事例調査 ボルト内の同じアプローチ]]
