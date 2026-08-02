---
title: "05_herdr+agmsgでintent-cli開発 スレッド分離オーケストレーション"
tags: [summary, ai, orchestration, herdr, agmsg, intent-cli, multi-agent]
source: https://x.com/tomohisa/status/2083660531886706743
author: tomohisa（@tomohisa）
published: 2026-08-02
created: 2026-08-02
---

# herdr+agmsg で intent-cli 開発 — スレッド分離オーケストレーション実例

> **Claude デスクトップアプリ内の設計スレッド（人間対話）と、herdr 内の3スレッド（オーケストレーション・設計・レビュー）を分離し、agmsg で繋ぐ実開発風景。レビューが request-update で PR に問題指摘 → 実装が修正を開始、という依頼→実装→検証のサイクルが回る。**

出典: [[63_herdr+agmsgでintent-cli開発 オーケストレーション実例（出典）]]（@tomohisa, 2026-08-02。本文はクリップ全文を使用。※短いポストのため情報量限定的・動画内容は未確認）

---

## 一行で

@tomohisa が herdr（ターミナルマルチプレクサ）+ agmsg（CLIエージェント間メッセージング）で intent-cli 自身を開発する様子を公開。スレッドを役割別に分離し、人間は設計スレッドのみ・実装の自動連鎖を herdr 内で回す実例。

## スレッド構成（核心）

| スレッド | 場所 | 役割 |
|---|---|---|
| **設計スレッド（人間対話用）** | Claude デスクトップアプリ内 | 人間と話す。作業指示・設計のみ。追加タスクの会話がいつでも可能 |
| **オーケストレーションスレッド** | herdr 内 | 全体調整 |
| **設計スレッド** | herdr 内 | 実装への作業指示・設計 |
| **レビュースレッド** | herdr 内 | PR への問題指摘（request-update）|

## 動作サイクル（ビデオ内の実例）

レビューが **request-update** で PR に問題指摘 → 実装が修正を開始、までの一連。依頼→実装→検証→差し戻しのサイクルが herdr 内で回る。

## 特徴

- **人間は設計スレッドだけ**を相手にする — 実装の細部に触れず、追加タスクの会話がいつでも可能（設計スレッドは作業指示・設計をするだけなので並行で会話できる）
- **実装の自動連鎖**は herdr 内の3スレッドで完結
- agmsg がスレッド間のメッセージ層。herdr がマルチプレクサ（複数エージェントの並列実行環境）

> [!note] 情報量について
> 本ポストは短く（3文）、詳細は動画内。レビューの request-update 機構・herdr 内スレッド間の具体的なメッセージ交換・intent-cli との連携の詳細は動画および各ツールのドキュメント要確認。

---

## 本ボルト内の位置付け — ccc の軽量実装版

本実例は **ccc（Claude-Code-Communication）の Redmine+Forgejo 型に対する、herdr+agmsg での軽量実装版**:
- **スレッド別役割分離** = ccc の5ロール（采/計/作/査/析）を、herdr の3スレッド（orchestration/設計/レビュー）で最小実装
- **レビュースレッド（request-update）** = ccc の査（合格/不合格二値判定）・[[03_Graph of Loops Claude Code完全システム10リポジトリ]] の L5 claude-review-loop（第2モデル署名までブロック）と同系
- **人間は設計スレッドのみ** = ccc の採（人間の決裁者）が対話する単一窓口
- 詳細は [[03_ccc関連事例調査 ボルト内の同じアプローチ]] 参照

## 既存ノートとの接続（3ツールの統合実例）

本ポストはボルト内の3ツールノートを統合する数少ない実例:
- **[[03_スマホからPCのcodexとccをリモート操作]]**（herdr）— ターミナルマルチプレクサ+Agent First。本ポストは herdr を「複数スレッドのコンテナ」として使う実例
- **[[02_agmsg CLIエージェント間メッセージング]]** — CLIエージェント間メッセージング。本ポストは agmsg の request-update による PR 問題指摘の実例
- **[[01_intent-system 意図駆動開発のオーケストレーションCLI]]** — intent-cli 自体。本ポストは intent-cli を intent-cli 自身で開発する（自己適用・dogfooding）実例

3ツールを組み合わせた「スレッド分離オーケストレーション」は、単独ツール紹介ノートでは見えない**統合的な運用パターン**を示す。

---

## 関連

- ccc の軽量実装版（スレッド分離） → [[03_ccc関連事例調査 ボルト内の同じアプローチ]]
- herdr（マルチプレクサ） → [[03_スマホからPCのcodexとccをリモート操作]]
- agmsg（メッセージング） → [[02_agmsg CLIエージェント間メッセージング]]
- intent-cli（開発対象） → [[01_intent-system 意図駆動開発のオーケストレーションCLI]]
- レビューの request-update → [[03_Graph of Loops Claude Code完全システム10リポジトリ]]（L5 claude-review-loop）
