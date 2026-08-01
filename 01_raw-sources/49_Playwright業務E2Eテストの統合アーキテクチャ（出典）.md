---
title: "49_Playwright業務E2Eテストの統合アーキテクチャ（出典）"
tags: [raw-source]
source: https://dev.classmethod.jp/articles/playwright-e2e-test-architecture-patterns/
author: lin-yuchen
published: 2026-07-31
created: 2026-08-01
---

# 出典メタデータ

- 記事URL: https://dev.classmethod.jp/articles/playwright-e2e-test-architecture-patterns/
- 著者: lin-yuchen（Developers.io / クラスメソッド）
- 公開: 2026年7月31日
- 形態: ブログ記事（技術解説・コード例多数）
- 環境: Playwright（`playwright` ライブラリ版・`@playwright/test` でない）+ TypeScript・業務 B2B ポータル

要約は [[11_Playwright業務E2Eテストの統合アーキテクチャ]] を参照。

> [!info] 本文取得について
> 本記事はユーザーが Clippings フォルダへクリップした全文を提供したため、それに基づき転記。

### 追加の出典: X ポスト（実務共感・2026-08-01 統合）

- ポストURL: https://x.com/connect24h/status/2083376797736108065
- 著者: connect24h（@connect24h）
- 公開: 2026年8月1日
- 形態: X ポスト（上記記事への実務共感・短）

> @connect24h: 「これは実務に刺さる。Playwright業務E2Eは、**書き方より構造で寿命が決まる**。私のE2E基盤もこれ。（中略）現場で怖いのは、**AIにテストを量産させて保守不能な重複まで増やすこと**。まず1業務フローだけこの構造で切り、Claude Code にも同じ責務分離を守らせてほしい。**Agent時代こそ保存して次のE2E設計で試す価値あり**。」

※#6（@connect24h）は本記事（#2）への短い実務共感ポストのため、出典ページに統合し重複ノート作成を回避。「書き方より構造で寿命が決まる」「AI にテスト量産させ保守不能な重複を増やすな」「Claude Code にも責務分離を守らせる」の3点は本記事の核心と一致。

---

# 本文（再構成）

## 課題: Page Object Model では業務フローが見えない

従来の Page Object Model（POM）はページ単位でメソッドを整理するが、複数ステップにまたがる業務フローのテストでは**各ステップの繋がりが見えにくい**。ポップアップで新しいページが開くと `page` オブジェクトが変わるがテストコード上で見えない。各メソッドが `void` を返すため**ステップ間依存関係が暗黙的**。

## 5つの統合パターン

### 1. Screen Object Model + Fluent Chaining

各メソッドが `Promise<OrderPortal>` を返し、`.then()` チェーンで繋ぐ。テストコードが仕様書のように読める:

```typescript
await OrderPortal.new(page, "stg-1")
  .then((portal) => portal.申込数量を選択する({ new: 1, upgrade: 3 }))
  .then((portal) => portal.商品カテゴリを選択する("スタンダード"))
  .then((portal) => portal.プランを選択する("スタンダード月額プラン"));
```

`return this` でも動くが `return new OrderPortal(this.page)` で**各ステップが独立状態を持つことを明示**。`.then()` チェーンは中間変数不要・インデント揃う・差分最小。

### 2. 日本語メソッド名

UI ラベルとコードを1対1対応させる。**QA チームが日本語ネイティブ**の現場で、テストコードを仕様書として読ませるため。`申込数量を選択する`・`商品カテゴリを選択する`・`プランを選択する` のように、UI の表示名と完全一致させる。

### 3. ロケーター関数辞書（遅延評価）

ロケーターを**関数として辞書化**し、呼び出し時に評価（遅延評価）。これにより、ポップアップで新しい `page` ができても、その `page` を関数に渡すだけで正しいロケーターが取得できる。文字列として保持すると新しい page で解決できない。

### 4. ポップアップウィンドウの確実なキャッチ

`waitForEvent` の順序問題を**ファクトリメソッドに封じ込める**。ポップアップが開く前に `waitForEvent('popup')` を仕掛けないと取り逃がすが、この順序依存を呼び出し側に見せない。ファクトリメソッド内で `Promise.all` 的に待機とクリックを協調させる。

### 5. 型安全な環境切り替え

`String Literal Union` で環境指定（`stg-1`/`stg-2` 等）を型で縛る。環境名のタイポを**コンパイル時に検出**。

## 核心: 書き方でなく構造で寿命が決まる

> @connect24h: Playwright業務E2Eは、**書き方より構造で寿命が決まる**。

画面変更のたびに selector 修正が各 spec へ飛び火する状態から、**業務操作を読めるテスト**へ変えられる。5パターンの統合で、selector 変更はロケーター辞書の1箇所、ポップアップ順序はファクトリの1箇所、環境切り替えは型の1箇所に閉じ込める。

> @connect24h: 現場で怖いのは、**AIにテストを量産させて保守不能な重複まで増やすこと**。まず1業務フローだけこの構造で切り、Claude Code にも同じ責務分離を守らせてほしい。**Agent時代こそ保存して次のE2E設計で試す価値あり**。
