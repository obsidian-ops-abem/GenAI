---
title: "99_intent-cli v0.6.2 クロスプロジェクト分離と検証済み生存（出典）"
tags: [raw-source]
source: https://github.com/J-Tech-Japan/intent-system/releases/tag/v0.6.2
author: J-Tech-Japan
published: 2026-08-02
created: 2026-08-02
---

# 出典メタデータ

- リリースURL: https://github.com/J-Tech-Japan/intent-system/releases/tag/v0.6.2
- リポジトリ: J-Tech-Japan/intent-system
- 形態: GitHub Release（パッチリリース）
- タイトル: **Release intent-cli v0.6.2**

> [!info] 既存ノートとの関係
> ボルト内に `13_intent-system v0.6.0（出典）` と `78_intent-cli v0.9.1（出典）` が存在。本ノートは v0.6.1 → v0.6.2 のパッチ詳細。

## 概要

v0.6.2 は v0.6.1 後にマージされた3スライス（G555・G556・G557）をカバーするパッチリリース。CLIサーフェス変更なし。

### G555: 共有マシンでのクロスプロジェクト分離

**事象（2026-07-29）**: 複数プロジェクトチームが同一マシンで稼働中、あるプロジェクトの設計スレッドが別プロジェクトのリソースを破損。

対策:
- **属性確認を変異の前に** — キー注入・プロセスkill・ワークスペース再構築の前に、4キー（workspace label・pane cwd・process cwd・agmsg `(team,role)` 命名）で所有権確立。**確認不能な属性は読み取り専用**
- **1チーム1ワークスペース**（チーム名でラベル、再利用・借用禁止）
- **チーム専用ロールフォルダ**
- **非破壊リカバリ** — 他プロジェクトの破損アーティファクトは保存・退避（破損しても所有者の証拠）。自分の分は新規再構築。**リカバリのデフォルトは recreate で cleanup ではない**

### G556: 検証済み生存（startup report ≠ readiness）

**事象**: 2つの codex エージェントが startup-complete レポートを送信後、数秒で死亡（リモート app-server の websocket transport reset）。

対策:
- **Verified liveness** — プロビジョニング完了 = startup report 到着 **かつ** settle delay 後に3点全通過: pane がagent TUIをホスト中 / agmsg ping-pong 往復成功 / codex bridge が安定 app-server に接続
- **早期死亡は正常モード** — TUI が shell prompt に落ちる（transport reset 後）。dialog-only scan はこれを見逃す
- **`agent-absent`** が監視pane-scanリストに追加。ルートは state 経由（dialog なし）。リカバリは shim-based relaunch + 完全 verified-liveness シーケンス再実行

---

## 関連（ボルト内）

- intent-system v0.6.0 → [[13_intent-system v0.6.0（出典）]]
- intent-cli v0.9.1 → [[78_intent-cli v0.9.1 グローバル名前空間の罠と修正（出典）]]
- herdr + agmsg での開発実例 → [[63_herdr+agmsgでintent-cli開発 オーケストレーション実例（出典）]]
