---
title: "50_Learn Claude Code ハーネスエンジニアリング学習（出典）"
tags: [raw-source]
source: https://x.com/DanKornas/status/2083332127546851776
repo: https://github.com/shareAI-lab/learn-claude-code
author: DanKornas (@DanKornas)
published: 2026-08-01
created: 2026-08-01
---

# 出典メタデータ

- ポストURL: https://x.com/DanKornas/status/2083332127546851776
- GitHub: https://github.com/shareAI-lab/learn-claude-code
- 紹介者: DanKornas（@DanKornas）— AI/ML/agents ニュースレター（dankornas.substack.com）
- 著者（リポジトリ）: shareAI-lab
- 公開: 2026年8月1日
- 形態: X ポスト（OSS 紹介）＋ GitHub README
- ライセンス: MIT
- キャッチコピー: "Bash is all you need"

要約は [[03_Learn Claude Code ハーネスエンジニアリング学習]] を参照。

> [!info] 本文取得について
> 本記事はユーザーが Clippings フォルダへクリップした全文（日本語ポスト）を提供。※ポストは紹介のみ、機能詳細は言及から再構成。

---

# 本文（再構成）

## コーディングエージェントがなぜ機能するのかを理解する

> コーディングエージェントがなぜ機能するのかを理解したいなら、**ハーネスを見る**のが役立つ —— プロンプトだけでなく。

**Learn Claude Code** は、エージェント環境がどのように組み立てられるかを学ぶビルダー向けの **0-to-1 ハーネスエンジニアリング学習プロジェクト**。基本的なループからより豊かな機能へとエージェントを追跡できる。**20の段階的なレッスン**で、一度に1つのハーネス機構を追加しながら。

### 主な特徴（5領域）

| 領域 | 内容 |
|---|---|
| **エージェントループ** | ツール呼び出しを実行し、結果をモデルに返す**実行可能なループ**から開始 |
| **ツール使用** | ループをそのままに保ちながら、**ハンドラーとディスパッチマップ**を追加 |
| **許可システム** | 境界・停止条件・承認要件をカバー |
| **コンテキストとメモリ** | スキル読み込み・コンテキスト圧縮・メモリ機構を含む |
| **エージェント操作** | タスクシステム・バックグラウンドタスク・チーム・ワークツリー分離・MCP をカバー |

オープンソース（MIT ライセンス）。

> コメント @HowToMoneyTruth: 「a fully functional coding agent demonstrating the entire architecture and interactions within it（アーキテクチャと相互作用全体を実演する完全機能のコーディングエージェント）」
