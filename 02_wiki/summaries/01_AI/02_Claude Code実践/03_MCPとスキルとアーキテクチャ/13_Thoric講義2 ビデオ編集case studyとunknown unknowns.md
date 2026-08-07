---
title: "13_Thoric講義2 ビデオ編集case studyとunknown unknowns"
tags: [summary, ai, claude-code, capability-overhang, unhobbling, unknowns, video-editing]
speaker: Thoric / Starick（Mike）（Claude Code チーム @ Anthropic）
created: 2026-08-07
---

# Thoric 講演2 — ビデオ編集 case study と unknown unknowns

> **AI はまだバリュエーションを獲得していない。モデルはスパイク状に賢くなり、capability overhang がある。ビデオ編集（color grading = shader という unknown unknown を可視化で削減）の case study が、unknown unknowns を減らして Claude を unhobble する具体例。ask_user_questions ツールの進化（1回→チェーン→HTML レポート）と「形式を切り替えると能力がアンロックされる」が Q&A の核心。**

出典: [[93_Thoric講義2 ビデオ編集case studyとunknown unknowns（出典）]]（Thoric / Claude Code チーム。本文はユーザー提供トランスクリプト tc/output/20260807_231216 から主題別に再構成。翌日の完成版講演 [[11_Thoric講演 Fableフィールドガイド unhobblingとunknowns]] の前日試論版・ビデオ編集 case study と Q&A が詳細）

---

## 一行で

Thoric による小規模イベント講演（ideas mid-formation）。AI のバリュエーション未達・capability overhang・unhobbling Claude を、**ビデオ編集で color grading の unknown unknowns を可視化で削減した case study** で具体化。Q&A で ask_user_questions ツールの進化史とプロトタイプ反復プロセスを詳述。

## 1. AI はまだバリュエーションを獲得していない

- ソフトウェア・知識労働は安価になった（明らか）
- だが**価値を生むのは依然困難**・スタートアップも困難
- **「正直、バリュエーションを獲得していない」** — 投資額に見合う価値をまだ生んでいない
- **「自分自身と交渉しない」** — 合理的でなく、現実にトレードオフを示させる。AI では good fast cheap が**トレードオフでないかもしれない**

## 2. Capability overhang と unhobbling

### モデルはスパイク状に賢くなる（直線でない）
- 3.5 Sonnet 時代の予想「コンテキストウィンドウ1億トークンでコードベース全体」→ **実際は tool calling・bash・grep で解いた**
- 有機的に育つ。どう賢くなるかはモデルを得るまで分からない

### ポケモンの例
- AW で終わるポケモン（1000匹中2匹）。ChatGPT は1匹。**Claude Code は全リストを取得して grep**。さらに Web アプリも生成可能
- 「問題が暗示する以上の豊かさ（abundance）」がある。だが平均ユーザーはより良いプロンプト/ツールに気づかない
- **Opus 3.5 / Fable には信じられない capability overhang**

### Claude の立場に立つ
- ユーザーが「コードベースについて」と聞いた時、Claude は技術レベル・規模・深さ・他の context（Git/Slack）・なぜ聞くかを探る必要
- 「上司に auth モジュールを説明してと言われたら『何について？』と聞くのと同じ」
- 明示的プロンプト例: **「経験豊富な TypeScript エンジニア・このモジュールは未知・高 compute 問題なので subagent を使って」**

## 3. ビデオ編集の case study（核心 — capability overhang の具体例）

> 「Claude は動画を編集できるか？」→ ほとんどの人は「ノー」。**だが Claude Code で実際にやっている**。

### プロセス
- 生素材（クリップ群）+ transcript を Claude へ
- transcribe・最適カット選択・UI/オーバーレイ生成・デザインシステム沿いの artifact 生成
- **「知識労働はますます、コードとスクリプトとデータのフォルダを agent が制御するものになりつつある」**

### Color grading — unknown unknowns の具体例
- 第1版と第2版で色が大きく違った。原因は **color grading**（生素材は overexposed raw・色を引き出すアートがある）
- Thoric 自身は color grading が **unknown unknowns だらけ**（知っていたのは ffmpeg・transcription・remo のみ）

### Unknown unknowns を削減するプロセス
1. Claude に color grading を説明させる → **レポートを読んでも理解できず**（「glaze over」現象・よくある）
2. **可視化を頼む** — vector scope・文献引用
3. **ピクセル毎に値がどう変わるかの可視化**を構築
4. **color grading = shader**（ピクセル入力→別色出力）というメンタルモデル獲得
5. 洞察: **肌色は背景と別に grade**（人間の肌色は動的範囲が狭く紫に見えると不自然・背景は紫でもOK）

> **unknown unknowns を削減することが、より良いものをより速く ship する鍵**

## Q&A — ask_user_questions ツールの進化（核心）

### ツール進化の4段階（Thoric が構築）
1. 当初: モデルが**1回だけ**呼べる
2. チェーン: 「インタビューして」で繋げる
3. 30-40問をまとめる
4. **現在: HTML レポートを構築し、はるかに深い回答を得る**

> **「形式を切り替える（markdown → HTML）と能力がアンロックされる」** — これを見つける科学はない。今は**アート（art）**

### プロトタイプ反復プロセス（Q&A）
- 「自分が何を欲しいか分からない」ことが多い — 実装の深い段階で発見（80%到達して「いや違う」）
- **iterative: spec → interview → prototype → プロトタイプ PR → 学びを取って reset → 再開**
- 安いプロトタイプ（HTML モックアップ等）で完全実装前に感覚を掴む

### Claude Tag
- 開始・ジョブ管理・**multiplayer（デフォルトで組織全体）**に極めて有効

---

## 本ボルト内の位置付け

本講演は [[11_Thoric講演 Fableフィールドガイド unhobblingとunknowns]]（翌日完成版）の**前日試論版**。同じ概念（capability overhang・unhobbling・unknown unknowns・being unreasonable）を扱うが:
- **ビデオ編集 case study がより詳細**（color grading = shader のメンタルモデル獲得・可視化で glaze over を突破）
- **ask_user_questions ツールの進化史**（1回→チェーン→HTML レポート・「形式を切り替えると能力アンロック」）は本講演独自
- **プロトタイプ反復プロセス**（spec→interview→prototype→reset）は spec 駆動の実践版

### 既存ノートとの接続
- **「形式を切り替えると能力がアンロック（markdown → HTML）」** は [[11_Thoric講演 Fableフィールドガイド unhobblingとunknowns]]（Markdown → Plan → HTML レポート）の詳細。**本ボルトも markdown（CLAUDE.md・index.md・Wikilink）から HTML への形式切り替えで能力が変わる可能性**を示唆
- **「知識労働はコードとスクリプトとデータのフォルダを agent が制御するものに」** は [[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]（file systems as memory）・本ボルトの3層構造と完全に一致。ビデオ編集という非コーディング領域での具体例が重要
- **color grading の unknown unknowns 削減** は [[04_Stop Vibe Coding Spec駆動開発の5ブロック]]（spec で unknowns を先取り）・[[05_claude-code-prompt-improver 送信瞬間に前提を補完]]（曖昧指示に質問）の具体実装。**「説明されても理解できない（glaze over）→ 可視化で突破」**は本ボルトの Query 操作（根拠付き回答）の質を上げる指針
- **「形式切り替えで能力アンロックは今はアート・科学でない」** は [[08_LangGraph Academy エージェント構築のコース]]「物理学でなく生物学に近い・経験的」・[[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]「モデルは生き物」と同根
- **プロトタイプ反復（spec→interview→prototype→reset）** は [[04_Stop Vibe Coding Spec駆動開発の5ブロック]]（5ステップループ）・[[02_Claude Code 計画と実行を分けるワークフロー]]（計画と実行の分離）・[[03_AI協業の発注の型 HITL実務]]（承認ポイントを絞る）の統合実践
- **「AI はまだバリュエーションを獲得していない・価値を生むのは困難」** は [[11_Thoric講演 Fableフィールドガイド unhobblingとunknowns]]「作るのは簡単だが価値を生むのは難しい」と同じ警告。本ボルト運用（知識整理）も「価値を生む」ことが目的

---

## 関連

- 翌日完成版講演（同概念の体系版） → [[11_Thoric講演 Fableフィールドガイド unhobblingとunknowns]]
- file systems as memory（知識労働=フォルダ制御） → [[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]
- spec 駆動（unknowns を先取り） → [[04_Stop Vibe Coding Spec駆動開発の5ブロック]]
- 曖昧指示に質問（ask_user_questions の実践） → [[05_claude-code-prompt-improver 送信瞬間に前提を補完]]
- 計画と実行の分離（プロトタイプ反復） → [[02_Claude Code 計画と実行を分けるワークフロー]]
- HITL承認ポイントを絞る → [[03_AI協業の発注の型 HITL実務]]
- 経験主義（アート・生物学に近い） → [[08_LangGraph Academy エージェント構築のコース]]
- モデルは生き物 → [[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]
