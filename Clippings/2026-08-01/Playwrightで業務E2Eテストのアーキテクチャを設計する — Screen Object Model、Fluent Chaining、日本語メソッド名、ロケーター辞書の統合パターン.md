---
title: "Playwrightで業務E2Eテストのアーキテクチャを設計する — Screen Object Model、Fluent Chaining、日本語メソッド名、ロケーター辞書の統合パターン"
source: "https://dev.classmethod.jp/articles/playwright-e2e-test-architecture-patterns/"
author:
  - "[[lin-yuchen]]"
published: 2026-07-31
created: 2026-08-01
description: "Playwright + TypeScriptで業務B2BポータルのE2Eテストを設計した際に採用した、Screen Object Model、Fluent Chaining、日本語メソッド名、ロケーター関数辞書、ポップアップ処理のファクトリメソッド封じ込めなど、複数パターンの統合アーキテクチャを紹介します。"
tags:
  - "clippings"
---
Playwright + TypeScriptで業務B2BポータルのE2Eテストを設計した際に採用した、Screen Object Model、Fluent Chaining、日本語メソッド名、ロケーター関数辞書、ポップアップ処理のファクトリメソッド封じ込めなど、複数パターンの統合アーキテクチャを紹介します。

## はじめに

業務系WebアプリケーションのE2Eテストを書いていると、テストコードが長くなり、何をテストしているのか一目でわかりにくくなることがあります。

従来のPage Object Model（POM）では、ページ単位でメソッドを整理しますが、複数ステップにまたがる業務フローのテストでは、各ステップの繋がりが見えにくくなりがちです。

今回は、実際の業務ポータルのテスト自動化プロジェクトで採用した、複数のパターンを統合したアーキテクチャを紹介します。

- **Screen Object Model + Fluent Chaining** — テストコードを仕様書のように読めるようにする
- **日本語メソッド名** — UIラベルとコードの1対1対応
- **ロケーター関数辞書** — 遅延評価でポップアップにも対応
- **ポップアップウィンドウの確実なキャッチ** — waitForEventの順序をファクトリメソッドに封じ込める
- **型安全な環境切り替え** — String Literal Unionで環境指定ミスをコンパイル時に防ぐ

## 前提・環境

- Playwright（ `playwright` ライブラリ版、 `@playwright/test` ではない）
- TypeScript
- 業務用ポータルサイト（複数ステップの申込フロー）
- 複数のステージング環境（stg-1、stg-2）
- QAチーム：日本語ネイティブのメンバーが中心
- `playwright` ライブラリ版を使用している理由：既存のNode.jsスクリプトに組み込む形で自動化しており、テストランナーの機能（ `@playwright/test` のアサーション、フィクスチャ等）は不要なため

> **注記**: 本記事のシステム名やプラン名は説明用の架空の名称です。

## 完成形：テストコードが仕様書になる

まず完成形を見てください：

```typescript
await OrderPortal.new(page, "stg-1")
  .then((portal) => portal.申込数量を選択する({ new: 1, upgrade: 3 }))
  .then((portal) => portal.商品カテゴリを選択する("スタンダード"))
  .then((portal) => portal.プランを選択する("スタンダード月額プラン"));
```

このコードは以下のように読めます：

1. stg-1環境でOrderPortalを開く
2. 新規1件、アップグレード3件を選択する
3. 商品カテゴリとして「スタンダード」を選択する
4. プラン「スタンダード月額プラン」を選択する

以降のセクションで、この完成形に至る各パターンを解説します。

![playwright-e2e-test-architecture-patterns-architecture](https://devio2024-media.developers.io/image/upload/f_auto/q_auto/v1785111463/2026/07/27/mxop73iev1qkzlms8jmr.png)

## 1\. Screen Object Model + Fluent Chaining

### 従来のPage Object Modelの課題

典型的なPOMでは、テストコードはこうなります：

```typescript
const portalPage = new OrderPortalPage(page);
await portalPage.selectQuantity(1, 3);
await portalPage.selectCategory("スタンダード");
await portalPage.selectPlan("スタンダード月額プラン");
```

一見問題なさそうですが、以下の課題があります：

- **ポップアップで新しいページが開く場合** 、 `page` オブジェクトが変わるが、それがテストコード上で見えない
- 各メソッドが `void` を返すため、 **ステップ間の依存関係** が暗黙的になる
- 非同期初期化が必要な場合、コンストラクタでは対応できない

### 各メソッドが Promise\<OrderPortal> を返す

Fluent Chainingの核心は、各メソッドが自分自身のクラスのインスタンスを返すことです：

```typescript
public async 申込数量を選択する(input: {
  new: number;
  upgrade: number;
}): Promise<OrderPortal> {
  await orderPortal.新規数量(this.page).fill(input.new.toString());
  await orderPortal.アップグレード数量(this.page).fill(input.upgrade.toString());
  await orderPortal.次へ(this.page).click();

  return new OrderPortal(this.page);
}
```

`return new OrderPortal(this.page)` で新しいインスタンスを返しています。 `return this` でも動きますが、新しいインスタンスを返すことで、各ステップが独立した状態を持つことを明示しています。

### .then() チェーン vs async/await

`async/await` でも同じことは書けます：

```typescript
const portal = await OrderPortal.new(page, "STG3");
const portal2 = await portal.申込数量を選択する({ new: 1, upgrade: 3 });
const portal3 = await portal2.商品カテゴリを選択する("スタンダード");
await portal3.プランを選択する("スタンダード月額プラン");
```

しかし、`.then()` チェーンの方が：

- 中間変数（ `portal2`, `portal3` ）が不要
- インデントが揃って視覚的に読みやすい
- 各ステップが1行で完結するため、仕様変更時の差分が最小になる

### return this vs return new OrderPortal(this.page) のトレードオフ

| 観点 | `return this` | `return new ClassName(this.page)` |
| --- | --- | --- |
| パフォーマンス | インスタンス生成なし | 毎回新規インスタンス |
| 状態の独立性 | 共有（副作用あり） | 独立（各ステップが独立） |
| デバッグ | 状態が上書きされる | 各ステップの状態を保持可能 |
| シンプルさ | シンプル | やや冗長 |

ページ遷移を伴うステップが多い場合は `return new OrderPortal(this.page)` を採用し、ページ遷移しない単純なフォーム操作だけなら `return this` で十分です。

## 2\. 日本語メソッド名 — UIラベルとの1対1対応

### 英語メソッド名の問題

```typescript
await portal.selectOrderQuantity({ new: 1, upgrade: 3 });
await portal.selectProductCategory("スタンダード");
```

`selectOrderQuantity` はUIに表示されている「申込数量」の英訳ですが、 **この対応関係はコードを読む人の頭の中にしかありません** 。QAメンバーがUIで見ている日本語ラベルと、コードの英語メソッド名の間に **メンタルマッピング** が必要です。

### 日本語にすると

```typescript
public async 申込数量を選択する(input: {
  new: number;
  upgrade: number;
}): Promise<OrderPortal> {
  await orderPortal.新規数量(this.page).fill(input.new.toString());
  await orderPortal.アップグレード数量(this.page).fill(input.upgrade.toString());
  await orderPortal.次へ(this.page).click();

  return new OrderPortal(this.page);
}

public async 商品カテゴリを選択する(
  input: "ベーシック" | "スタンダード" | "プレミアム"
): Promise<OrderPortal> {
  // ...
}
```

TypeScriptはUnicode識別子を完全にサポートしており、技術的な制約はありません。コンパイルエラーも型チェックも英語の識別子と全く同じように機能します。

### 使い分けの基準

すべてのメソッド名を日本語にしているわけではありません：

**日本語にするもの（業務フローのメソッド）：**

- UIラベルと直接対応するアクション： `申込数量を選択する` 、 `プランを選択する`
- ロケーターキー： `見積もり作成に進む` 、 `新規数量` 、 `次へ`

**英語のままにするもの（インフラ層のメソッド）：**

- ページ遷移： `toHome()`
- ログイン： `login()`
- デバイス選択（UIラベルが英語）： `selectDevice()`

基準はシンプルです： **UIに表示されているラベルの言語に合わせる** 。

## 3\. ロケーター関数辞書 — 遅延評価で複数ページに対応

### よくある実装の問題

```typescript
// セレクタ文字列を定数にするパターン
const SELECTORS = {
  nextButton: 'button:has-text("次へ")',
  newQtyInput: 'input[name="newQty"]',
};

await page.locator(SELECTORS.nextButton).click();
```

問題点：

- `page.getByRole()` や `page.getByText()` のようなセマンティックなロケーター戦略を使えない
- ポップアップで新しい `page` が生まれた場合、 **どの `page` に対してロケーターを適用するかが曖昧**

### 関数辞書パターン

ここで `orderPortal` （小文字）はロケーター辞書オブジェクト、 `OrderPortal` （大文字）はScreen Objectクラスです。辞書はデフォルトエクスポートなので、インポート時に小文字にして区別しています。

```typescript
import { Page } from "playwright";

const orderPortal = {
  見積もり作成に進む: (page: Page) =>
    page.getByRole("link", {
      name: "見積もり作成に進む（新規タブで開く）",
    }),

  // 申込数量
  新規数量: (page: Page) => page.locator('input[name="newQty"]'),
  アップグレード数量: (page: Page) => page.locator('input[name="upgradeQty"]'),
  次へ: (page: Page) => page.getByRole("button", { name: "次へ" }),

  // 商品カテゴリ
  ベーシック: (page: Page) => page.getByText("ベーシック", { exact: true }),
  スタンダード: (page: Page) => page.getByText("スタンダード"),
  プレミアム: (page: Page) => page.getByText("プレミアム"),
};

export default orderPortal;
```

### なぜ「関数」なのか

**遅延評価：** ロケーター関数は呼び出されるまで実行されません。辞書定義時点ではpageが存在しなくてもOKです。

**異なるページコンテキストに対応：** ポップアップウィンドウが開くと、新しい `Page` オブジェクトが生まれます。関数辞書なら、どの `page` に対してもロケーターを適用できます：

```typescript
// メインページで
await orderPortal.見積もり作成に進む(mainPage).click();

// ポップアップページで（同じロケーター辞書を使える）
await orderPortal.次へ(popupPage).click();
```

### ロケーター戦略の優先順位

辞書内で複数のロケーター戦略を混在させています：

1. `getByRole` — ARIAロール＋アクセシブルネーム（最も安定）
2. `getByText` — 表示テキスト（UIラベルが安定している場合）
3. `locator('[name="..."]')` — HTML属性（フォーム要素で確実）
4. `locator('.css-xxxxx')` — CSSクラス（最終手段）

辞書にまとめておくことで、各ロケーターがどの戦略を使っているかが一覧できます。

### ファイル構成

画面ごとにファイルを分けます：

```
class/locator/
├── orderPortal.ts   # 申込ポータルのロケーター
└── adminPortal.ts   # 管理画面のロケーター
```

## 4\. ポップアップウィンドウの確実なキャッチ

### よくある失敗パターン

```typescript
// ダメな例：クリック後にリスナーを登録
await page.goto(url);
await page.getByRole("link", { name: "見積もり作成に進む" }).click();

// クリック後にpopupを待つ → 既にpopupイベントが発火済みで取りこぼす
const newPage = await page.waitForEvent("popup"); // タイムアウト！
```

`click()` が完了した時点で、ブラウザはすでにポップアップを開いています。その後に `waitForEvent("popup")` を呼んでも、イベントは過去のものなのでキャッチできません。

![playwright-e2e-test-architecture-patterns-popup-sequence](https://devio2024-media.developers.io/image/upload/f_auto/q_auto/v1785111789/2026/07/27/dtilb2u4kgmrushhtwwa.png)

### 正しいパターン：リスナーを先に登録し、ファクトリメソッドに封じ込める

```typescript
public static async new(page: Page, env: "stg-1" | "stg-2") {
  const url = env === "stg-1" ? PORTAL_URL_STG1 : PORTAL_URL_STG2;

  // 1. ポップアップのPromiseを先に取得（まだawaitしない）
  const newPagePromise = page.waitForEvent("popup");

  // 2. ページ遷移とクリック
  await page.goto(url);
  await orderPortal.見積もり作成に進む(page).click();

  // 3. ここでawait — クリックで発火したpopupイベントをキャッチ
  const newPage = await newPagePromise;

  // 4. 新しいページでインスタンスを生成
  return new OrderPortal(newPage);
}
```

ファクトリメソッドに封じ込めれば、呼び出し側はポップアップの存在を意識する必要がありません：

```typescript
const portal = await OrderPortal.new(page, "stg-1");
await portal.申込数量を選択する({ new: 1, upgrade: 3 });
```

### ページコンテキストの切り替え

ポップアップが開くと、2つの `Page` オブジェクトが存在します。 `OrderPortal.new()` の中で `return new OrderPortal(newPage)` としているため、以降の操作はすべてポップアップ側で行われます。

### waitForEvent の他の用途

`popup` 以外にも、同じ「リスナーを先に登録、アクションを後で実行」パターンが使えます：

```typescript
// ダウンロード
const downloadPromise = page.waitForEvent("download");
await page.getByRole("button", { name: "ダウンロード" }).click();
const download = await downloadPromise;

// ダイアログ（alert/confirm/prompt）
page.on("dialog", async (dialog) => {
  await dialog.accept();
});
await page.getByRole("button", { name: "削除" }).click();
```

## 5\. 型安全な環境切り替え

### String Literal Unionで環境指定ミスを防ぐ

ファクトリメソッドの `env` パラメータに注目してください：

```typescript
public static async new(page: Page, env: "stg-1" | "stg-2") {
```

`env: "STG1" | "STG3"` — これだけで：

- `OrderPortal.new(page, "stg1")` → コンパイルエラー
- `OrderPortal.new(page, "STG2")` → コンパイルエラー
- `OrderPortal.new(page, "stg-1")` → OK

IDEのオートコンプリートで有効な値が表示されるため、ドキュメントを見に行く必要もありません。

### 他のアプローチとの比較

| アプローチ | 型安全性 | 簡潔さ | 拡張性 | 適するケース |
| --- | --- | --- | --- | --- |
| String Literal Union | ○ | ◎ | △ | 環境数が2-3個 |
| enum | ○ | △ | ○ | チーム全体で共有する定数 |
| 設定オブジェクト + `keyof typeof` | ○ | ○ | ◎ | 環境数が多い、URLマッピングが必要 |

環境数が少ない（2-3個）テスト自動化プロジェクトでは、String Literal Unionが最もバランスが良く、環境数が増えてきたら設定オブジェクトパターンへ自然に移行できます。

## 全体のファイル構成

```
class/
├── screen/
│   ├── OrderPortal.ts   # Screen Object（Fluent Chaining + ファクトリ）
│   └── AdminPortal.ts   # 別画面のScreen Object
├── locator/
│   ├── orderPortal.ts   # 申込ポータルのロケーター辞書
│   └── adminPortal.ts   # 管理画面のロケーター辞書
└── wrapper/
    ├── Playwright.ts     # ブラウザ起動のラッパー
    └── Jira.ts           # 外部サービス連携
scripts/
└── example.ts           # テストスクリプト本体
```

## まとめ

- **Screen Object + Fluent Chaining** で、`.then()` チェーンによるテストコードが仕様書のように読める
- **日本語メソッド名** で、UIラベルとコードの対応関係が一目でわかる（業務フロー層は日本語、インフラ層は英語）
- **ロケーター関数辞書** で、遅延評価と複数ページ対応を実現し、セレクタの一元管理ができる
- **ポップアップ処理をファクトリメソッドに封じ込める** ことで、 `waitForEvent` の順序ミスを防ぐ
- **String Literal Union** で、環境指定ミスがコンパイルエラーになる

これらのパターンは個別でも使えますが、統合することで「テストコードを読めば業務フローがわかる」レベルの可読性が得られます。複数ステップの業務フローをテストする際に、このアーキテクチャを試してみてください。