---
title: "15_OmniAppワークショップ マルチモーダルAIエージェント"
tags: [summary, ai, multimodal, omniapp, google-adk, gemini-live, mcp, parallel-agent, websocket]
source: Google Cloud Space Class workshop（Annie / Google Cloud エンジニア）
author: Annie（Google Cloud エンジニア）
published: 2026-08-04
created: 2026-08-04
---

# OmniApp ワークショップ — マルチモーダル AI エージェント

> **OmniApp（多感覚入力・推論・多感覚出力のソフトウェア）を3レベルで構築するハンズオン。express（Gemini Image でアバター生成・in-context conditioning で一貫性）→ perceive&reason（マルチエージェント並列投票・MCP server/BigQuery MCP）→ live（Gemini Live API・full-duplex WebSocket・live request queue）。従来型 STT→LLM→TTS の逐次パイプラインを raw audio 双方向ストリーミングで置換。**

出典: [[87_OmniAppワークショップ マルチモーダルAIエージェント（出典）]]（Annie, 2026-08-04。本文はユーザー提供の文字起こし全文を使用・ASRで"John Isaac it"=use_jsx/"fast MCP"=FastMCP 等の誤変換）

---

## 一行で

Google Cloud の Annie による3時間ハンズオン。OmniApp（perceive→reason→express を many senses で・live で同時）を3レベルで構築：アバター生成・マルチエージェント並列投票・双方向リアルタイム。Gemini Live API が raw audio のまま処理し STT/TTS 逐次パイプの遅延を排除。full-duplex WebSocket と ADK の live request queue が「寿司コンベア」のように上流/下流を完全分離。

## OmniApp の定義（核心）

```
PERCEIVE (many senses) → REASON (planning/tools/memory) → EXPRESS (many senses)
                              ↑ 加えて "live" = 同時・双方向
```

従来の text-in/text-out エージェント（画像もテキストキャプションに変換して処理）と違い、**body と eyes を持つ**。カメラ・マイクを持ち、発話と視覚を同時処理。

## Level 0: Express（アバター生成・一貫性）

- Gemini 3.0 Image でアバター生成（色・外見の記述から）
- **一貫性の鍵：in-context conditioning**。同じ chat session 内で生成すると、モデルが前のターンの出力画像を文脈の一部として取り込み、次ターンで「自分の描いたもの」を見て整合
- **4段階の一貫性手法**（軽→重）：(1) 強い prompt 反復 (2) 同一 session（本ワークショップ）(3) 参照画像 (4) fine-tuning
- **variable injection リスク**：ユーザー入力を直接 prompt に埋め込むと prompt injection。Model Armor で防御

## Level 1: Perceive & Reason（マルチエージェント投票）

3つの専門エージェントが並列で証拠を分析・投票：
- **geological agent**（FastMCP ツール・画像分析）
- **botanical agent**（FastMCP ツール・動画分析）
- **astronomical agent**（**Google Managed BigQuery MCP**・データベースクエリ）

### MCP server（自作 vs Google Managed）
- **自作**：FastMCP で `@tool` デコレータ定義→Cloud Run にデプロイ
- **Google Managed**：BigQuery 等、認証/接続/セキュリティを Google が管理。URL 指定だけ

### ADK 1.x parallel agent（並列投票）
- 3専門エージェントを parallel agent で同時実行→aggregator が結果統合
- **投票で意思決定**（例: 2/3 が "fossilized" → fossilized 確定）
- 並列の利点：1エージェントの誤りを他が補正。欠点：コスト・複雑性増

### callback と session state（重要概念）
- **before_agent callback**：エージェント開始前に state を setup（URL 等をロード）
- **session state**：エージェント間・ツール間で共有するスクラッチ変数。callback が書き→agent/tool が読む
- ADK 2.0 では graph workflow で自由にカスタマイズ可能

## Level 3: Live（双方向リアルタイム）

### 従来パイプラインの問題
```
audio → STT → text → LLM → text → TTS → audio
```
各ステップで遅延が累積。会話のラグが顕著。

### Gemini Live API（核心）
- **raw audio in / raw audio out**（STT/TTS を挟まない）
- 声のトーン・ためらい・熱量も処理
- **full-duplex**（電話・同時双向・途中割込可）vs half-duplex（トランシーバー・順番）

### full-duplex WebSocket パイプライン
```
Browser ──(audio 60ms毎)──→ WebSocket ──→ Live Request Queue
                                                    ↓ (FIFO)
                          ADK Runner ←── Gemini Live API
                              ↓
Browser ←──(audio/video)── WebSocket
```

### **live request queue（寿司コンベア）**
- upstream（ブラウザ→キュー）と downstream（キュー→Gemini→ブラウザ）が**完全分離**
- どちらも相手を待たない。一方が遅くても他方を止めない
- 2種類の入力：real-time（連続ストリーム・音声/動画）と send-content（離散・スナップショット/テキスト）

### セッションサービス
- in-memory（即時・再起動で消失）・database（SQL/Spanner/Graph 等）・agent platform（Google Cloud 管理）
- live 体験では in-memory が最速で推奨

## 関連

- [[10_マルチエージェントでナレッジグラフ構築 Neo4j×Google ADK]] — 別の Google ADK コース。本ワークショップはその ADK のマルチモーダル/live 拡張
- [[12_AIエージェントのメモリシステム 4層構造とRAG]] — working memory・session state の基礎。本ワークショップの state 管理の前提
- [[09_ジェネレータ×エバリュエータハーネスで長時間走るエージェント]] — compaction と clean context。本ワークショップの in-memory セッションは逆に揮発性を選ぶ
- [[14_Memory Engineer 4研究所の記憶設計15ステップ]] — Nvidia の KV cache 視点。Gemini Live のストリーミングも同様のハードウェア制約
- [[08_LangGraph Academy エージェント構築のコース]] — 別フレームワークの並列/状態管理。本ワークショップは ADK 版

## 所感

「STT→LLM→TTS の逐次パイプを raw audio で置換」は、遅延問題の根本解決。live request queue の「寿司コンベア」比喩は upstream/downstream 分離を直感的に説明。parallel agent の投票方式は [[09_ジェネレータ×エバリュエータハーネス]] の adversarial 構造とは違う「合議制」アプローチ（批評でなく投票）。in-context conditioning でアバター一貫性を保つ技法は、プロンプトベースで手軽に試せる点が実用的。Google Managed MCP で BigQuery 等への接続を簡略化する方向は、[[09_SKILL.md入門 新人研修マニュアル]] の「3段階 lazy load」と共に、インフラの隠蔽トレンド。
