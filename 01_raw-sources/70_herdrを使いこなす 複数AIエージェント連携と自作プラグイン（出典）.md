---
title: "70_herdrを使いこなす 複数AIエージェント連携と自作プラグイン（出典）"
tags: [raw-source]
source: https://blog.techscore.com/entry/2026/08/03/080000
author: techscore
published: 2026-08-03
created: 2026-08-03
---

# 出典メタデータ

- URL: https://blog.techscore.com/entry/2026/08/03/080000
- 著者: techscore（シナジーマーケティング株式会社）
- 公開: 2026年8月3日
- リポジトリ: https://github.com/ogulcancelik/herdr
- 公式ドキュメント: https://herdr.dev/docs/

---

# 本文

![](https://cdn-ak.f.st-hatena.com/images/fotolife/t/techscore/20260731/20260731140726.png)

## はじめに

この記事では、herdr のインストールと基本操作から始めて、複数の AI エージェントを並行運用するときの「困りごと」と、それを herdr の機能でどう解決するかを紹介します。後半では、自作プラグインで herdr を自分のワークフローに合わせて拡張する方法も扱います。

本記事は herdr v0.7.5（2026-07-21 リリース）時点の情報です。 herdr は 1.0 前のバージョンで、バージョン間で CLI に破壊的変更が入ることがあります。お使いのバージョンの [公式ドキュメント](https://herdr.dev/docs/) もあわせて確認してください。

---

## インストールと基本機能

### インストール

herdr は 1 つのバイナリで動きます。インストールは次のいずれかです。

```bash
# インストールスクリプト（Linux / macOS）
curl -fsSL https://herdr.dev/install.sh | sh

# または Homebrew
brew install herdr
```

起動は `herdr` だけです。 設定は `~/.config/herdr/config.toml` に置きます（Windows は `%APPDATA%\herdr\config.toml` ）。 ファイルが無ければ `herdr --default-config > ~/.config/herdr/config.toml` で雛形を作れます。

### 基本の用語

| 用語 | 意味 |
| --- | --- |
| サーバー / クライアント | herdr はサーバー常駐型。クライアント（画面）を閉じてもサーバー側でエージェントは動き続ける |
| セッション | サーバーが保持する作業全体のまとまり。名前を付けて複数持てる |
| ワークスペース | プロジェクト単位の作業空間（サイドバーでは「spaces」）。1 つのセッションに複数並ぶ |
| タブ | ワークスペース内の画面を切り替える単位。1 つのワークスペースに複数並ぶ |
| ペイン | タブ内の画面分割。ペイン 1 つが 1 つの端末になる |
| エージェント | ペインの中で動く AI コーディングエージェント（Claude Code や Codex など） |
| サイドバー | 左側に出る、ワークスペースとエージェントの状態の一覧 |

### 基本操作

操作感は tmux に近く、 `prefix` （既定 `Ctrl+b` ）を押してからキーを続けて押します。

| 操作 | キー |
| --- | --- |
| ペインを左右に分割 | `prefix+v` |
| ペインを上下に分割 | `prefix+minus` |
| ペイン間を移動 | `prefix+h/j/k/l` |
| ペインをズーム | `prefix+z` |
| 新しいタブ | `prefix+c` |
| サイドバーの表示切り替え | `prefix+b` |
| キーバインド一覧を表示 | `prefix+?` |

### サイドバーで状態を把握する

各ペインでエージェントを起動すると、その状態（working / idle / blocked / done）が自動で検出され、サイドバーに色分けで一覧されます。

| 状態 | 意味 |
| --- | --- |
| `working` | 処理を進めている |
| `blocked` | 承認や質問の UI が出ていて、人の応答を待っている |
| `idle` | 入力待ち。そのタブは表示済みで、こちらが状況を把握できている |
| `done` | 入力待ち。ただし作業の完了をまだ見ていない（タブを表示すると idle になる） |

### Git worktree を扱う

| 操作 | コマンド |
| --- | --- |
| 一覧を見る | `herdr worktree list --cwd <repo>` |
| 作成して開く | `herdr worktree create --cwd <repo> --branch feature-a` |
| 既存の worktree に切り替える | `herdr worktree open --cwd <repo> --branch feature-a` |
| 削除する | `herdr worktree remove --workspace <id> --force` |

### 名前付きセッションを使い分ける

| コマンド | 説明 |
| --- | --- |
| `herdr --session <name>` | 名前付きセッションを起動 / 再接続 |
| `herdr session list` | セッション一覧（名前・状態・ディレクトリ） |
| `herdr session attach <name>` | 別のセッションに接続を切り替える |
| `herdr session stop <name>` | セッションごと停止 |
| `herdr session delete <name>` | 停止済みセッションを削除 |

---

## 別のエージェントにプロンプトを送信する

`herdr agent prompt` を使うと、別のペインで動いているエージェントにプロンプトを直接送信し、そのまま実行させられます。

```bash
# 送り先のペイン ID を調べる
herdr pane list --workspace <id>

# そのペインのエージェントにプロンプトを送る
herdr agent prompt <pane_id> "<プロンプト本文>"
```

### コマンドを覚えなくても、自然な言葉で頼める

いま作業しているエージェント自身が herdr の CLI を呼べるので、「左のペインのエージェントに、この方針を伝えておいて」と自然な言葉で頼むだけで実行してくれます。

| 頼みたいこと | 裏で使われる CLI |
| --- | --- |
| 「ペインを分割して別の作業を並べて」 | `herdr pane split` |
| 「そこでエージェントを起動して」 | `herdr agent start` |
| 「あのエージェントが終わるまで待って」 | `herdr agent wait` |
| 「このコマンドを隣のペインで流して」 | `herdr pane run` |
| 「終わったら通知して」 | `herdr notification show` |

---

## herdr の操作をエージェント自身に任せる

段取りそのものを 1 体のエージェントに任せられます。本記事では、この取りまとめ役を **lead**（リード＝司令塔役）、実作業を分担する側を **helper**（ヘルパー＝手伝い役）と呼びます。

公式の [Agent skill](https://herdr.dev/docs/agent-skill/) をエージェントに読み込ませると、そのエージェントは「ペインを増やす」「そこに別のエージェントを起動する」「終わるまで待つ」といった herdr の操作を、自分の判断で行えるようになります。

導入は 3 ステップです。

```bash
# 1. Agent skill を（グローバルに）インストールする
npx skills add ogulcancelik/herdr --skill herdr -g

# 2. lead を起動するペインの ID を調べる
herdr pane list --workspace w1 | jq -r '.result.panes[] | "\(.pane_id)\t\(.foreground_cwd)\t\(.terminal_title_stripped // "-")"'

# 3. skill を読み込んだ lead エージェントを、そのペインで起動する
herdr agent start lead --kind claude --pane w1:p1
```

**あなたが用意するのは lead 1 体だけ** です。helper は、lead が指示を受けてから `herdr` の CLI を使って自分で作ります。

---

## エージェントの完了を待って次の作業を自動で流す

| やりたいこと | コマンド |
| --- | --- |
| ペインを分割する | `herdr pane split <pane_id> --direction right` |
| ペインの ID を確認する | `herdr pane list --workspace <id>` |
| ペインでエージェントを起動する | `herdr agent start <name> --kind <kind> --pane <pane_id>` |
| エージェントの完了を待つ | `herdr agent wait <target>` |
| 別のエージェントに指示を送る | `herdr agent prompt <target> <text>` |
| ペインでコマンドを実行する | `herdr pane run <pane_id> <command>` |
| 出力を待ち受ける | `herdr pane wait-output <pane_id> --match <text>` |
| 通知する | `herdr notification show <title>` |

```bash
# 実装エージェントの完了を待ってテストを起動し、失敗したら通知する
herdr agent wait "$IMPL"
herdr pane run "$TEST" "sh -c 'npm test && echo DONE'"
herdr pane wait-output "$TEST" --match DONE || herdr notification show "test failed"
```

---

## どの端末からでも同じ作業環境に戻る

herdr はサーバー常駐型です。クライアントを閉じてもサーバー側でエージェントは動き続けます。

| やりたいこと | 操作 |
| --- | --- |
| デタッチする（切り離す） | `prefix+q` |
| 再接続する | `herdr` （名前付きは `herdr session attach <name>` ） |
| 別マシンから接続する | `herdr --remote <ssh-target>` |
| 再起動後もエージェントを復元する | config.toml で `resume_agents_on_restore = true` （既定で有効） |

---

## 足りない機能はプラグインで自作する

herdr のプラグインは特別な SDK を必要とせず、 **実行ファイルとマニフェストだけ** でできています。

### 最小のプラグインを作ってみる

```bash
#!/usr/bin/env bash
# wt.sh — 今のリポジトリに worktree を 1 つ切る
set -eu
cwd="$(printf '%s' "$HERDR_PLUGIN_CONTEXT_JSON" | jq -r '.focused_pane_cwd')"
herdr worktree create --cwd "$cwd" --branch "wt/$(date +%m%d-%H%M%S)" --focus
```

```toml
# herdr-plugin.toml
id = "me.wt"
name = "wt"
version = "0.1.0"
min_herdr_version = "0.7.5"

[[actions]]
id = "new"
title = "現在のリポジトリに worktree を切る"
command = ["bash", "wt.sh"]
```

### 実例: ディレクトリを選んで workspace を作る

候補のディレクトリを集めて選ばせ、選んだパスを `herdr workspace create --cwd` に渡します。`zoxide` や `ghq` といったユーザーが既に使っている仕組みをそのまま候補の取得元にします。

### 実例: エージェントの状態変化に自動で反応させる

`pane.agent_status_changed` イベントを使えば、入力待ちや完了になった瞬間、そのペインへ自動でフォーカスを移せます。

```toml
[[events]]
on = "pane.agent_status_changed"
command = ["bash", "autofocus.sh"]
```

---

## 相性のいいツール

保存のたびに自動で差分追従する [hunk](https://github.com/modem-dev/hunk) や、差分に行コメントを付けてエージェントへ送り返す [herdr-reviewr](https://github.com/persiyanov/herdr-reviewr) など。

---

## まとめ

エージェントへのプロンプト送信、段取りごと任せるエージェント連携、完了待ちの自動化、セッションの永続化。 herdr があれば、複数の AI エージェントを使う開発をターミナルの中で回せます。そのうえ、足りない機能は自作プラグインで足せます。 herdr は「与えられた機能を使うツール」ではなく、「自分で拡張していくツール」です。
