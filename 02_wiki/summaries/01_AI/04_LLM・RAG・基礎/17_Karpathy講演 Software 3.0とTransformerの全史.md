---
title: "17_Karpathy講演 Software 3.0とTransformerの全史"
tags: [summary, ai, karpathy, software-3, prompt, llm, transformer, attention, history]
speaker: Andrej Karpathy（元 OpenAI/Tesla・2023年2月時点で OpenAI 復帰直後）
published: 2023-02（TreeHacks キーノート＋Q&A）
created: 2026-09-28
---

# Karpathy 講演 — Software 3.0 と Transformer の全史

> **Software 1.0=アルゴリズムを設計（70年）／2.0=データセットを設計（NN訓練=コンパイル・重み=バイナリ）／3.0=プロンプトを設計。GPT は「実行時に自然言語プログラムを実行するよう再構成できる汎用コンピュータ」で、プログラム=プロンプト、実行=文書完了。「最も難しい新しいプログラミング言語は英語」。Q&A で attention 起源（Bahdanau・中学の翻訳練習の視線）・「他を削除し注意だけ残す」・nanoGPT の内部まで。**

出典: [[115_Karpathy講演 Software 3.0とTransformerの全史（出典）]]（Karpathy / TreeHacks 2023-02。本文はユーザー提供トランスクリプトから主題別に再構成。※2023年2月の歴史的基礎資料）

---

## 一行で

Karpathy による ハッカソン キーノート＋延伸 Q&A。Software 1.0/2.0/3.0 のパラダイム史を提示し、「プロンプト=プログラム・英語=プログラミング言語」を宣言。Q&A で Transformer/attention の起源史と nanoGPT の内部を「コミュニケーション相＋計算相」として解説する。

## 核心 — ソフトウェアの3パラダイム

| パラダイム | 設計するもの | 対応関係 |
|---|---|---|
| **Software 1.0**（70年） | アルゴリズム | ソース→コンパイル→バイナリ |
| **Software 2.0** | データセット | **データセット→NN訓練（=コンパイル）→重み（=バイナリ）**。データエンジン（デプロイ→テレメトリ→収集→ラベル→反復） |
| **Software 3.0** | プロンプト | **GPT=汎用コンピュータ。プロンプト=プログラム。実行=文書完了** |

- 2.0 は 1.0 を置き換えず**上に重なる**（2.0 をコンパイルするのに 1.0 が必要）
- 「**最も難しい新しいプログラミング言語は英語**」
- **プロンプトは人間のプログラミング方法でもある** — 技術が人間に収束

## プロンプト設計の実例（キーノード）

- **CoT の原点**: "Let's work this out in a step-by-step way to be sure we have the right answer." でベンチマーク82%
- **IQ 200 条件付け**: ChatGPT はインターネットの**平均**を模倣。「IQ 200 の人に説明して」と絞ると遥かに良い
- **ChatGPT を仮想マシンに**: 「Linux ターミナルとして振る舞え」→ 幻のファイルシステム・`cat` で自分の「書いた」内容を参照・**Python を言語モデルの頭の中で実行**し正解
- **スマートホームを英語でプログラム**: 機器と家の配置を英語で宣言 → 「20分読書させたら照明を消して」→ タイムスタンプ計算済みの正しい JSON コマンド
- **GPT-only バックエンド**（Scale ハッカソン最優秀）: バックエンドの Python を完全削除し単一 LLM。state（JSON）+ルート → 新 state。**英語でデータに任意の操作**
- **Sydney のプロンプトリーク**: 英語だけで人格をインスタンス化していた

## Q&A の核心

### Transformer の歴史（分野統一と attention 起源）
- 2007年: 特徴量記述子の動物園+SVM。**分野毎に異なる語彙** → 2012 AlexNet で「計算とデータ」へ統一
- 2003 MLP言語モデル → 2014 seq2seq（LSTM）→ **エンコーダボトルネック** → 2014 Bahdanau 注意（ソースをソフト検索で見返す）
- **Dmitri Bahdanau の起源メール**: 「**中学生の翻訳練習で source と target を行き来する視線から着想**」
- 2017: **「他を全部削除し、注意だけ残す」**。ResNet 構造・LayerNorm・マルチヘッド・4x 拡張。定着した変更は pre-norm 化程度で**5年間頑健**
- **in-context learning = メタ学習**: 活性化の中で勾配降下に似た学習（例が増えるほど精度向上）

### attention の別解釈（コミュニケーション相＋計算相）
- Transformer = **コミュニケーション相（マルチヘッド注意=有向グラフ上のデータ依存メッセージパッシング）**＋**計算相（MLP=ノード毎）**
- **query=探しているもの／key=持っているもの／value=伝えるもの**。内積→softmax→重み付き和
- 因果マスク=未来からの情報遮断。自己注意と交差注意の違いは**key/value の出所のみ**
- **RNN は「長く薄いグラフ」、Transformer は「浅く広いグラフ」**（最適化可能性・並列性）
- 後知恵の論文タイトル: "**General-purpose, efficient, optimizable computer is all you need**"

### scratchpad とメモリ
- scratchpad トークンで覚えたいことを書かせ、後で参照 — **「人間がノートを使うのと同じ」**。頭に保持=context、ノート=外部メモリ

### nanoGPT
- 300行の完全実装。block size=最大文脈（実効バッチは batch×時間）。埋め込み+位置符号を**加算**。生成時は文脈満杯で**クロップ**（これがコンテキスト長の正体）

---

## 本ボルト内の位置付け

- **本ボルトの原型との直結**: 本ボルトの運用ルール（3層・Ingest/Query/Lint）の直接の原型は Karpathy の LLM Wiki gist（[[04_カーパシーのObsidian活用術 30分で第二の脳]]・[[01_Claude×Obsidianで第二の脳を作る]]）。本講演はその同一人物による「**なぜプロンプトでプログラムできるのか**」の概念的土台
- **「プロンプト=プログラム」** は [[03_AIエージェントの正体はプロンプトだった]]（エージェントの正体は高度に構造化されたプロンプト）の源流。Software 3.0 の定式化そのもの
- **「英語=プログラミング言語」** は [[04_Stop Vibe Coding Spec駆動開発の5ブロック]]（spec=エージェント向けの実行計画）・[[05_claude-code-prompt-improver 送信瞬間に前提を補完]]（曖昧指示の自動補完）の理論的背景。**英語がプログラムなら、曖昧な英語=バグのあるプログラム**であり、spec 駆動や prompt-improver はその「型検査/コンパイラ」に相当
- **「平均の模倣を避けスライスに絞る（IQ 200 条件付け）」** はプロンプト設計の基本原理として [[11_LLM数学基礎 トークン化からTransformerまで]] の実務側の対
- **Transformer の数学・歴史** は [[11_LLM数学基礎 トークン化からTransformerまで]]（attention の数学）を補完する**起源と直感的解釈**（コミュニケーション相/計算相・query/key/value の意味論・Bahdanau の視線着想）
- **scratchpad=人間のノート** は [[10_Memory Engineering 最も見過ごされる層]]（hierarchical retrieval: working memory→検索）・[[12_AIエージェントのメモリシステム 4層構造とRAG]]・本ボルトの index.md（working memory）/Wikilink（ノート参照）設計と同一思想の**2023年時点の原型**
- **データエンジン** は [[06_自己改善型AIトレード機械 6段階ループと未解決の6番目]]（Backtest→Post-mortem→Fine-tune のループ）・[[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]（dreaming）の祖先型。**Software 2.0 のデータエンジンが、3.0 では dreaming 的な記憶整備に移行しつつある**と読める
- **「GPT=汎用コンピュータ・NN=特殊用途」** は [[01_Jev入門 System One Modelの日本語解説 npaka]]・[[04_Jevは誰のためのモデルか 汎用Decision Model]] と対になる構図 — Karpathy の言う「汎用」テキストコンピュータに対し、Jev は「汎用**決定**コンピュータ」への分岐
- **ChatGPT 仮想マシン（幻のファイルシステム）** は [[03_AIエージェントの正体はプロンプトだった]] の「シミュレータとしての LLM」の実演

---

## 関連

- プロンプト=エージェントの正体 → [[03_AIエージェントの正体はプロンプトだった]]
- 本ボルトの原型（Karpathy LLM Wiki） → [[04_カーパシーのObsidian活用術 30分で第二の脳]]
- spec=英語のプログラム（型検査） → [[04_Stop Vibe Coding Spec駆動開発の5ブロック]]
- 曖昧指示の自動補完 → [[05_claude-code-prompt-improver 送信瞬間に前提を補完]]
- Transformer の数学 → [[11_LLM数学基礎 トークン化からTransformerまで]]
- scratchpad=ノート・記憶の階層 → [[10_Memory Engineering 最も見過ごされる層]]・[[12_AIエージェントのメモリシステム 4層構造とRAG]]
- データエンジン→dreaming（自己改善ループ） → [[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]・[[06_自己改善型AIトレード機械 6段階ループと未解決の6番目]]
- 汎用 vs 特殊（Jev への分岐） → [[01_Jev入門 System One Modelの日本語解説 npaka]]
- モデル選定の基礎 → [[07_Everything Fable 5 Mythosクラスとプロンプトガイド]]
