---
title: "15_Claude Managed Agents進化 トークンから本番エージェントへ"
tags: [summary, ai, anthropic, managed-agents, agent-sdk, sandbox, deployment]
source: Anthropic 製品エンジニア ライトニングトーク
speaker: Anthropic 製品エンジニア
created: 2026-10-04
---

# Claude Managed Agents 進化 — トークンから本番エージェントへ

> **3段階の進化: Message AI（トークン出入力）→ Agent SDK（ループ/コンテキスト/サンドボックス）→ Managed Agents（ハーネス/永続化/認証/スケール全部込み。タスクと設定だけ持ってくる）。キーは脳（ループ）と手（サンドボックス）の分離 — 全ユースケースがサンドボックスを必要とせず必要時だけ起動。「プロトタイプ数時間→本番数週間」の数週間を削る。**

出典: [[122_Claude Managed Agents進化 トークンから本番エージェントへ（出典）]]（Anthropic 製品エンジニア。本文はユーザー提供トランスクリプト tc/output/20260929_075951 から主題別に再構成）

---

## 一行で

Anthropic のエージェント提供形態の進化（Message AI→Agent SDK→Managed Agents）と、Managed Agents の3リソース・脳/手分離・SRE エージェント実装例を解説するライトニングトーク。

## 進化の3段階

| 段階 | 提供物 | 残る自作 |
|---|---|---|
| **Message AI** | トークン in/out | ループ・コンテキスト・ツール実行・状態復旧・認証・観測性・全部 |
| **Agent SDK**（Claude Code の一般化） | ループ・コンテキスト管理・ツール実行・サンドボックス・FS | ホスティング・スケール・セッション状態・観測性 |
| **Managed Agents** | 目的構築ハーネス・ランタイムサンドボックス・セッション永続化/チェックポイント・認証・ボールト・ホスティング・スケール・観測性 | **タスク・設定・カスタムツールロジックだけ** |

## 核心 — 脳と手の分離
- 従来: ループとツール実行が同一コンテナ→**起動レイテンシ・単一障害点**
- Managed Agents: **脳（モデル/ループ）と手（サンドボックス/ツール）を分離**。サンドボックスは**必要な時だけ起動**→スケール・低レイテンシ・高信頼

## 3リソース
**エージェント設定**（バージョン管理・ロールバック可）＋**インフラ/ガードレール**（コンテナ・ネットワーク境界・許可ホスト）＋**セッション**（=設定×環境の起動）

## 機能一覧
- **会話はクラウド**（ラップトップを閉じても継続・イベントで制御）・マルチセッション永続化
- **階層メモリ**＋**dreaming**（トランスクリプトから学習しスキルとメモリを改善）
- **outcomes**（複数回反復で高品質・Ralph 的）・**vaults**（クレデンシャル保管）
- MCP・ウェブフック・権限ポリシー・インタラプト
- **コンソールのエージェントビルダー**（平文記述→本番グレード設定）
- **scheduled/triggered agents**（朝8時ダイジェスト等）

## SRE エージェント例
インシデント→エージェント定義（永続化）→コンテナ/境界→ログ/スキルをマウント→セッション開始→**本物のサンドボックスで grep/glob し根本原因**→**このコードがそのまま本番**（同一 agent ID/環境）

---

## 本ボルト内の位置付け

- **[[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]（Applied AI 視点の dreaming/managed agents）の製品エンジニア視点の対**。Lmas が述べた「production 4原則（versioning/hashing/permissioning/portability）と dreaming API」がどの製品機能に対応するかの直接の答え（versioning=設定の版管理・portability=セッション/イベント API・dreaming=dreaming）
- **脳と手の分離** は [[07_Harness Engineering 壊れないAIエージェントの作り方]]（脳/手/履歴の分離）の**そのままの実装**。同講演の引用する Anthropic Managed Agents（session/harness/sandbox）を本トークが具体化
- **「Agent SDK=Claude Code の一般化」「裏側は Message AI」** は [[05_Claude Codeの6層アーキテクチャ ダムループ]]（ダムループ+周囲の層）・[[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]（ハーネス）の提供形態面での整理
- **scheduled/triggered agents** は [[05_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]]（nightly compiler）・Boris 講演（loops/routines）のマネージド版
- **「プロトタイプ数時間→本番数週間」の数週間を削る** は [[02_24時間自走する自律型AIエージェントの設計図]]（本番投入前チェック）・ccc（Forgejo Actions で自作した配備）と比較して**自作ハーネス vs マネージドのトレードオフ**を示す。ccc は自作側の実例
- **SRE エージェント（サンドボックス内で grep/glob）** は [[04_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤]]（サンドボックス基盤）のマネージド対極

---

## 関連

- dreaming と production 4原則 → [[12_Lmas講演 Context EngineeringとMemory Systemsの祭具]]
- 脳/手/履歴の分離 → [[07_Harness Engineering 壊れないAIエージェントの作り方]]
- Agent SDK の正体 → [[05_Claude Codeの6層アーキテクチャ ダムループ]]・[[07_Boris Cherny 講演 Claude Codeハーネスとproduct overhang]]
- スケジュールド（nightly compiler） → [[05_ULTIMATE SECOND BRAIN 第二の脳の新しい失敗モード]]
- 自作ハーネス側の実例（対比） → [[03_ccc関連事例調査 ボルト内の同じアプローチ]]
- サンドボックス基盤（自作側） → [[04_ephemeral-sandbox 並列エージェント用OSSサンドボックス基盤]]
