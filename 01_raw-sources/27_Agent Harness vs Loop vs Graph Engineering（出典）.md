---
title: "21_Agent Harness vs Loop vs Graph Engineering（出典）"
tags: [raw-source]
source: https://x.com/LunarResearcher/status/2082076425465762082
article_url: https://x.com/i/article/2082042903384489984
author: Lunar (@LunarResearcher)
published: 2026-07-28
created: 2026-07-31
reconstructed: true
---

# 出典メタデータ

- ポストURL: https://x.com/LunarResearcher/status/2082076425465762082
- 記事URL（X article）: https://x.com/i/article/2082042903384489984
- 著者: Lunar（@LunarResearcher）— ceo @kollectivexyz / AI engineer
- 公開: 2026年7月28日 12:11（JST）
- 形態: X article（長文）。メインポストは見出し＋リンクのみ
- エンゲメント: 18 RT / 104 いいね / **251 ブックマーク** / 140Kビュー
- タイトル: **Agent Harness Engineering vs Loop Engineering vs Graph Engineering**

要約は [[08_Agent Harness vs Loop vs Graph Engineering]] を参照。

> [!warning] 本文取得状況
> 本ポストは X の「article」形式でログイン壁があり、**本人の本文全文を直接取得できなかった**。下記「再構成内容」は以下の情報源から高い精度で再構成したもの：
> - 記事タイトル・リプライ群（@iiiichigo_chan / @0xWast3 / @mycomputerspot）の直接引用
> - Vin Vashishta「Harness, Loop, & Graph: A Simple Explanation」（bug-fix エージェントの具体例・3層構造）
> - MindStudio「Loop Engineering vs Harness Engineering」（what/when vs where/what touch/how recover の対比定義）
> - Medium（@bijit211987）/ Towards AI / TrueFoundry / DevCompass の複数独立解説が一致して述べる定義
>
> 3つの概念の定義は情報源間で完全に一致しているため、再構成の確度は高い。正確な引用が必要な場合は原文（要ログイン）を参照のこと。

---

# 再構成内容

## リプライ群から読み取れる論点

メインポストへの主要リプライ:

- **@iiiichigo_chan**: "i think loop engineering is the most important but graph engineering gets more and more important too"（Loop が最重要だが Graph の重要性も増している）
- **@0xWast3**: "I think every agent is good"
- **@mycomputerspot**: "The layers matter. If files and APIs are involved, 'agent' stops being a feature name and starts being an ops problem."（**ファイルやAPIが絡むと、agent は機能名ではなく運用問題になる**）

最後のリプライは本記事の核心を突く: エージェントが本物のシステム（ファイル・API操作）に触れると、Harness 層（環境・インフラ・復旧）が運用上の本質になる、という指摘。

## 3つの概念の定義（複数情報源で一致）

| 概念 | 責務 | 一言定義 | 種類 |
|---|---|---|---|
| **Harness Engineering** | 環境・インフラ・復旧 | モデルが動く**環境**を構築する | 基盤レイヤー（制御フローとは独立） |
| **Loop Engineering** | 反復の制御フロー | 作業→フィードバック→反復の**サイクル**を設計する | 制御フロー（線形・順次） |
| **Graph Engineering** | グラフ構造の制御フロー | ノードとエッジで**エージェント間の接続**を定義する | 制御フロー（非線形・分岐） |

**重要**: Loop と Graph は**制御フロンの代替選択**（タスクごとにどちらかを選ぶ）だが、**Harness はそのどちらとも併用する別の基盤レイヤー**。

### Harness Engineering（環境・インフラ層）

- **焦点**: エージェントの環境とインフラストラクチャ
- **定義（MindStudio）**: 「loop が *what the agent does / when* を定義するなら、harness は ***where it runs / what it can touch / how it recovers*** を定義する」
- **責務**: メモリ管理、ツールルーティング、ロギング、セキュリティ、サンドボックス、エラー復旧
- **成果物**: パイプライン、ツール設定、観測性ダッシュボード、エラーハンドラ
- **Vin Vashishta の定義**: LLM は本質的にテキストを生成するだけで外部システムに触れられない。「モデルと世界の間に座る翻訳層」が harness。モデル出力を実際の関数（ファイルを開く等）に変換し、結果をモデルが読めるテキストに戻す
- **怠ると**: 完璧な loop があっても、不十分な harness では「本番で壊れる・エラーから復旧できない・観測不能」になる

### Loop Engineering（反復制御フロー）

- **焦点**: 時間経過に伴うエージェントの挙動
- **定義**: work → feedback → iterate の反復サイクル。各パスで何をすべきか、ケイデンス、いつタスク完了かを制御
- **成果物**: 終了条件、反復ロジック、ケイデンスルール
- **怠ると**: 明確な完了基準がないため「永遠にループする・止まる・早すぎる停止」

### Graph Engineering（グラフ構造制御フロー）

- **焦点**: 複雑・大規模タスクでのタスク分散
- **定義**: 単一ループエージェントが詰まる/記憶過多になる問題を、**複数の特化ステップと明確なハンドオフ**に分割
- **成果物**: ノード（作業単位）、エッジ（ルーティング）、共有状態
- 単一ループは「自分自身を指すエッジを持つ1ノードのグラフ」に過ぎない（→ [[07_Graph Engineering Clearly Explained]] と整合）

## 具体例: バグ修正エージェント（Vin Vashishta）

| 層 | 役割 |
|---|---|
| **Harness** | エージェントにファイルを開く・編集する能力を与える翻訳層。ツール・サンドボックス・観測性 |
| **Loop** | コード変更がテストを壊したら、学んだことを適用して再試行。「次に何を、いつ止めるか」 |
| **Graph** | 単一エージェントが詰まったら、ジョブを複数の特化ステップ（再現→分析→修正→検証）に分割しハンドオフ |

## 3層は「競合」ではなく「同じシステムの異なる部分」

Vin Vashishta の明確な主張:

> "in reality they are different parts of the same system"（現実には、これらは同じシステムの異なる部分にすぎない）

人々はしばしば3つを**競合する方法論**として語るが、実際は**1つの階層的アーキテクチャ**として協調する。さらに、これらの層は企業に AI エージェントへの**段階的移行パス**を提供し、技術的な一気作り変えではなくスムーズな移行を可能にする。

## いつどちらの制御フローを選ぶか

- **Loop を選ぶ**: 線形・順次なタスク、単一エージェントで完結する場合。まず Loop から始めるのが基本（→ [[05_ループエンジニアリング14ステップ]]）
- **Graph を選ぶ**: 非線形・分岐・並列が多い、複数特化ステップ間で状態共有が必要、単一ループが記憶過多/待ち状態に陥る場合
- **Harness は常に必要**: どちらを選んでも、本番運用には環境・復旧・観測性の harness が必須

LangChain は Graph Engineering を「確立されたアプローチの最新の名称」と位置づけ、Loop/Harness 工学と繋いでいる。

---

## 関連（ボルト内の系譜）

本記事は、本ボルト既存の3層/5層モデル群と同じ系譜:

- [[02_LOOP vs GRAPH vs HARNESS ENGINEERING]] — 同じ3層の診断フレーム（@0xwhrrari）
- [[04_Prompt to Graph Engineering 5層の統一モデル]] — Prompt/Context/**Harness**/Loop/Graph の5層（@akshay_pachaar 前編）
- [[07_Graph Engineering Clearly Explained]] — Graph 層を掘り下げた続編（@akshay_pachaar）
- [[03_LOOP→GRAPH→HARNESSパイプラインを一気通貫で実装する]] — 3層の実装チュートリアル
- [[05_ループエンジニアリング14ステップ]] — Loop 層のロードマップ
