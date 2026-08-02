---
title: "34_Vibe Kanban コーディングエージェント用Kanbanワークスペース（出典）"
tags: [raw-source]
source: https://x.com/DanKornas/status/2083026345060167825
repo: https://github.com/BloopAI/vibe-kanban
author: Dan Kornas (@DanKornas)
published: 2026-07-31
created: 2026-08-01
---

# 出典メタデータ

- ポストURL: https://x.com/DanKornas/status/2083026345060167825
- GitHub: https://github.com/BloopAI/vibe-kanban
- 著者: Dan Kornas（@DanKornas）— AI/ML/agentsニュースレター（dankornas.substack.com）
- 公開: 2026年7月31日
- 形態: X ポスト（ツール紹介）
- ライセンス: Apache License 2.0
- **注意**: README によると **Vibe Kanban はサービス終了予定**（Bloop 本体は2026年4月に無料ユーザーによる収益化圧迫でシャットダウン、コミュニティが維持）

要約は [[01_Vibe Kanban コーディングエージェント用Kanbanワークスペース]] を参照。

> [!info] 本文取得について
> 本記事はユーザーが Clippings フォルダへクリップした全文を提供したため、それに基づき転記。

---

# 本文（再構成）

## 病因: ターミナルタブ間の計画問題

> ターミナルタブ間でコーディングエージェントの作業を計画すると、**実装よりもレビューが難しくなる**ことがある。

## Vibe Kanban とは

コーディングエージェントが行う作業の**計画とレビュー**を支援する、開発者向けの **Kanban ベースのワークスペース**。各エージェントのワークスペースに専用のブランチ・ターミナル・開発サーバーを割り当て、イシューからレビュー済みの変更へ移行しやすくする。

## 主な機能

- **Kanban イシュー** —— ボード上で作業を作成・優先順位付け・割り当て
- **エージェントワークスペース** —— エージェントにブランチ・ターミナル・開発サーバーを提供
- **差分レビュー** —— インラインコメントを残し、エージェントにフィードバック送信
- **アプリプレビュー** —— 組み込みブラウザ、DevTools、インスペクトモード、デバイスエミュレーション
- **エージェント選択** —— Claude Code、Codex、Gemini CLI を含む **10以上のコーディングエージェント**を切り替え

## コメント欄の洞察

- **@i_mika_el**: 「Inline diff feedback tied to each agent workspace is the piece terminal tabs never solve.」（各エージェントワークスペースに紐付いたインライン差分フィードバックこそ、ターミナルタブが決して解決しない部分）
  - **@DanKornas の応答**: 「The diff is the handoff point. If review still lives in another tab, the workspace has only moved the mess around.」（差分が引き継ぎポイント。レビューが別タブのままなら、ワークスペースは散らかりを動かしただけ）
- **@RaoulDukeDegen**: 「bloop shut down april cuz free users killed monetization but community keeps it」（Bloop は4月にシャットダウン、コミュニティが維持）

## 位置付け

ターミナルタブでエージェントを並走させると「どのエージェントが何をやったか」「差分レビュー」が困難になる問題を、**Kanban + 専用ワークスペース（ブランチ/ターミナル/開発サーバー分離）+ インライン差分レビュー**で解決する。差分レビューをワークスペース内に閉じ込める点が核心（@DanKornas 自身が指摘）。

---

## 関連（ボルト内）

- エージェント並列実行のワークスペース分離 → [[02_1チャットをエージェントチームへ Opus5 12ステップ]]（worktree隔離）・[[29_A Graph of Loops（仮）]]（agent-worktree G2）
- エージェント運用の全体設計 → [[02_24時間自走する自律型AIエージェントの設計図]]
