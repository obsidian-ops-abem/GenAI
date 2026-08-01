---
title: "03_intent-system 意図駆動開発のオーケストレーションCLI"
tags: [記事まとめ, AI, agent-engineering, oss, loop]
source: https://github.com/J-Tech-Japan/intent-system/releases/tag/v0.6.0
created: 2026-07-29
status: 未着手
---

# intent-system: 意図駆動開発のオーケストレーションCLI (v0.6.0)

> [!info] 出典
> [J-Tech-Japan/intent-system](https://github.com/J-Tech-Japan/intent-system) v0.6.0リリース（GitHub、Apache-2.0）
> 詳細メタデータ: [[13_intent-system v0.6.0（出典）]]

GitHub上で「意図駆動開発（intent-driven development）」を回すための決定論的な支援ツール（CLI名: `intent-cli`）。C#/.NET 10、日英両方のドキュメントを持つ。

## 設計思想: AIを運転席に残したまま暴走させない

このツール自体は Claude や Copilot などのAIエージェントを**一切起動しない**（"intent-cli never launches Claude, Codex, or any other AI provider"）。代わりに、AIエージェント側が「今どのフェーズにいて、次に何を決めるべきか」をこのツールに問い合わせ、明示的で検査可能なコマンドを通じて状態を管理する。AIエージェントは常に運転席にあり、intent-cliは地図・信号機の役割に徹する、という位置づけ。

## v0.6.0の変更点

| 項目 | 内容 |
|---|---|
| オーケストレーションモデル | 試験的だった「4スレッド・メッセージ型オーケストレーション」が正式な推奨デフォルトに昇格。タイマーループ型も引き続き完全サポート |
| チーム運用ガイダンス | ターミナルワークスペースの立ち上げ手順、デザインスレッドによる監督権限の境界、セッションの生存周期と再起動時の再武装ルールを明文化 |
| 停滞検知の完成（3種新規） | `backlog-ready-idle`（作業が空のまま既定45分放置）／`blocked-label-drift`（ブロック状態とGitHubラベルの不整合）／`repair-stalled`（修復PRが180分動かず昇格対象に） |
| 新コマンド | `automation runs-audit`（実行ログの整合性検証）／`queue priority-drift`（優先度分類の監視）／`automation issue-block`（ブロック状態のGitHub標準化） |
| データ永続性 | `queue-state.json`への同時書き込みで項目が丸ごと消える問題を修正。古い状態の読み込みを検出して再適用し、意図しない削除は書き込み自体を中止する |
| 廃止された手作業 | タイトル規約の手動確認、ドメイン情報の重複記載、キューステートの手編集、停滞修復の手動通知——これらが不要に |

**破壊的変更が3点**: `stalled-work`のレポート形式、修復ラベルの判定方法、クロスドメイン検証の挙動。

```bash
dotnet tool install -g JTechJapan.IntentSystem.Cli --version 0.6.0
```

## 所感・関連

- [[05_ループエンジニアリング14ステップ]] の理論がそのまま実装として出てきた印象。特に「状態ファイル」（`queue-state.json`の永続化・整合性保護）、「Ralph Wiggumループ（静かに失敗するループ）」への対策（停滞検知3種）、「人間の承認ゲート」（AIを運転席に残しintent-cli自身はAIを起動しない設計）が具体的に対応している
- `blocked-label-drift`（内部状態とGitHubラベルの不整合検知）は、状態を単一のソースオブトゥルースに保つ工夫として実務的
- 4スレッド・メッセージ型オーケストレーションが「タイマーループ型」に代わる新デフォルトになった、という変遷自体が、ループ設計のトレードオフ（イベント駆動 vs 定期実行）の実例として参考になる

## 次にやること

- [ ] 実際に `intent-cli` を試し、停滞検知の設定（`backlog-ready-idle`の45分など）が自分のワークフローに合うか確認する
- [ ] このボルトの `log.md`（状態ファイル）と `queue-state.json` の設計思想を比較する

関連: [[index]] / [[05_ループエンジニアリング14ステップ]] / [[02_LOOP vs GRAPH vs HARNESS ENGINEERING]]
