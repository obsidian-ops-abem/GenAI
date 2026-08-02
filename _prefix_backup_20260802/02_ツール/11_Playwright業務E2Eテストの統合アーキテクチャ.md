---
title: "11_Playwright業務E2Eテストの統合アーキテクチャ"
tags: [summary, tool, playwright, e2e-testing, typescript, architecture, screen-object-model]
source: https://dev.classmethod.jp/articles/playwright-e2e-test-architecture-patterns/
author: lin-yuchen
published: 2026-07-31
created: 2026-08-01
---

# Playwright 業務 E2E テストの統合アーキテクチャ

> **「書き方より構造で寿命が決まる」** — Page Object Model の課題を、Screen Object Model + Fluent Chaining + 日本語メソッド名 + ロケーター関数辞書 + ポップアップ ファクトリ封じ込めの5パターン統合で解く。テストコードが仕様書になる。

出典: [[49_Playwright業務E2Eテストの統合アーキテクチャ（出典）]]（lin-yuchen, Developers.io, 2026-07-31。本文はユーザー提供クリップから再構成。@connect24h の実務共感ポストを追加出典として統合）

---

## 一行で

業務 B2B ポータルの E2E テスト設計で、従来の Page Object Model（POM）の課題（ポップアップで page が変わる・ステップ間依存が暗黙的・業務フローが見えない）を、**5つのパターン統合**で解決。各メソッドが `Promise<OrderPortal>` を返し `.then()` チェーンで繋ぐことで**テストコードが仕様書のように読める**。

## 核心: 書き方でなく構造で寿命が決まる

> @connect24h: Playwright業務E2Eは、**書き方より構造で寿命が決まる**。私のE2E基盤もこれ。

画面変更のたびに selector 修正が各 spec へ飛び火する状態から、**業務操作を読めるテスト**へ変える。selector 変更は辞書の1箇所、ポップアップ順序はファクトリの1箇所、環境切り替えは型の1箇所に閉じ込める。

## 5つの統合パターン

| # | パターン | 解く問題 |
|---|---|---|
| 1 | **Screen Object Model + Fluent Chaining** | 各メソッドが `Promise<OrderPortal>` を返し `.then()` チェーン。ステップ間依存を明示化・中間変数不要・差分最小 |
| 2 | **日本語メソッド名** | UI ラベルとコードを1対1対応。QA チーム（日本語ネイティブ）がテストコードを仕様書として読める |
| 3 | **ロケーター関数辞書（遅延評価）** | ロケーターを関数として保持。ポップアップで新しい page ができても page を渡すだけで解決（文字列だと解決不能） |
| 4 | **ポップアップ ファクトリ封じ込め** | `waitForEvent('popup')` の順序依存をファクトリメソッドに隠蔽。呼び出し側に順序を見せない |
| 5 | **型安全な環境切り替え** | `String Literal Union` で環境名（stg-1/stg-2）を型で縛り、タイポをコンパイル時に検出 |

## 完成形: テストコードが仕様書になる

```typescript
await OrderPortal.new(page, "stg-1")
  .then((portal) => portal.申込数量を選択する({ new: 1, upgrade: 3 }))
  .then((portal) => portal.商品カテゴリを選択する("スタンダード"))
  .then((portal) => portal.プランを選択する("スタンダード月額プラン"));
```

これが自然言語の仕様書として読める: 「stg-1 で OrderPortal を開く → 申込数量を新規1・アップグレード3で選択 → 商品カテゴリをスタンダードに → プランをスタンダード月額に」。

## `return this` vs `return new OrderPortal(this.page)`

`return this` でも動くが、`return new OrderPortal(this.page)` で**各ステップが独立状態を持つことを明示**。ポップアップで page が変わった場合も、新しいインスタンスが新しい page を保持する。

## 環境

- **Playwright**（`playwright` ライブラリ版・`@playwright/test` でない）—— 既存 Node.js スクリプトに組み込む形で自動化しており、テストランナー機能（アサーション・フィクスチャ）不要
- **TypeScript**・業務用ポータル（複数ステップ申込フロー）・複数ステージング環境

---

## 本ボルト内の位置付け

**テスト設計のアーキテクチャ作法**。02_ツール カテゴリだが、単なるツール紹介でなく**設計論**。AI エージェント運用の複数ノートと「責務分離・構造で寿命が決まる」思想が通底:

- **「書き方より構造で寿命が決まる」** は [[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]（you do not fix the code, you fix the process）・[[03_AI協業の発注の型 HITL実務]]（個別でなくパターンへ注意）と同根。「場所」でなく「構造」に注目する作法
- **「AI にテストを量産させて保守不能な重複を増やすな」**（@connect24h）は、AI エージェントにコード生成させる際の品質ガバナンス。[[01_エージェントファクトリの作り方 ビルダーズガイド]]（no evals, no production）・[[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]（判定器・ルールブック）のテスト版。AI が量産するテストにも**責務分離の構造**が必要
- **「Claude Code にも同じ責務分離を守らせる」**（@connect24h）は、AI エージェントにテストを書かせるなら**この5パターンの構造を CLAUDE.md/ルールブックに明記して従わせる**という実務指針。[[04_カーパシーのObsidian活用術 30分で第二の脳]]（事務作業を AI に渡す）や [[10_agent-skill-creator ワークフローをAIスキル化]]（定型作業のスキル化）のテスト適用例
- **ロケーター関数辞書（遅延評価）・ポップアップ ファクトリ封じ込め** は、変更影響を1箇所に閉じ込める「境界の明確化」。[[03_Graph Engineering with Claude 14-Step roadmap]]（node 契約: 境界ある入出力）や [[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]（state を境界に）の設計思想と同じ
- **日本語メソッド名**（UI ラベルと1対1）は、[[03_AI協業の発注の型 HITL実務]]（結論ファースト・構成を先に承認）のように「コードを人間の認識に合わせる」作法。テストコード＝仕様書

## 関連

- 構造で寿命・プロセス改善 → [[04_自己レビューエージェントのGraph設計 Anthropicメソッド]]・[[03_AI協業の発注の型 HITL実務]]
- AI 生成の品質ガバナンス → [[01_エージェントファクトリの作り方 ビルダーズガイド]]
- AI エージェントに責務分離を守らせる → [[04_カーパシーのObsidian活用術 30分で第二の脳]]・[[10_agent-skill-creator ワークフローをAIスキル化]]
- 境界の明確化（node 契約） → [[03_Graph Engineering with Claude 14-Step roadmap]]
