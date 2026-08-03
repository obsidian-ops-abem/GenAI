---
title: "herdr を使いこなす: 複数 AI エージェントの連携から自作プラグインまで"
source: "https://blog.techscore.com/entry/2026/08/03/080000"
author:
  - "[[techscore]]"
published: 2026-08-03
created: 2026-08-03
description: "herdr の基本操作から、複数 AI エージェントの連携・自動化・セッション永続化まで実例で紹介します。足りない機能をプラグインで自作する方法も扱います。"
tags:
  - "clippings"
---
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

この記事を読み進めるのに必要な言葉を、先に整理しておきます。

| 用語 | 意味 |
| --- | --- |
| サーバー / クライアント | herdr はサーバー常駐型。クライアント（画面）を閉じてもサーバー側でエージェントは動き続ける |
| セッション | サーバーが保持する作業全体のまとまり。名前を付けて複数持てる |
| ワークスペース | プロジェクト単位の作業空間（サイドバーでは「spaces」）。1 つのセッションに複数並ぶ |
| タブ | ワークスペース内の画面を切り替える単位。1 つのワークスペースに複数並ぶ |
| ペイン | タブ内の画面分割。ペイン 1 つが 1 つの端末になる |
| エージェント | ペインの中で動く AI コーディングエージェント（Claude Code や Codex など） |
| サイドバー | 左側に出る、ワークスペースとエージェントの状態の一覧 |

実際の画面では、左にサイドバーがあり、右側にワークスペース（その中のタブとペイン）が並びます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/t/techscore/20260731/20260731140832.png)

### 基本操作

操作感は tmux に近く、 `prefix` （既定 `Ctrl+b` ）を押してからキーを続けて押します。キーの表記は config.toml の記法に合わせて `prefix+v` のように書きます。 `prefix` に続く `+` は「続けて押す」、 `shift+n` のような修飾キーの `+` は「同時押し」を表します。よく使うキーは次のとおりです。

| 操作 | キー |
| --- | --- |
| ペインを左右に分割 | `prefix+v` |
| ペインを上下に分割 | `prefix+minus` |
| ペイン間を移動 | `prefix+h/j/k/l` |
| ペインをズーム | `prefix+z` |
| 新しいタブ | `prefix+c` |
| サイドバーの表示切り替え | `prefix+b` |
| キーバインド一覧を表示 | `prefix+?` |

キーを覚えきれないうちは `prefix+?` が便利です。現在割り当てられているキーバインドと設定の一覧がその場で表示されるので、記事や公式ドキュメントを開き直さずに確認できます。

ワークスペース（サイドバーの「spaces」）の移動は次のキーです。

| 操作 | キー |
| --- | --- |
| ワークスペースを選んで移動 | `prefix+w` |

一方、個々のエージェント（agents）へ直接移動するキーは既定では未割り当てです。必要なら config.toml で `focus_agent` （例 `prefix+alt+1..9` ）や `next_agent` を割り当てます。

ワークスペースは Git worktree だけでなく、任意のフォルダに対して作れます。 `--cwd` に開始ディレクトリを渡すだけです。

| 操作 | コマンド |
| --- | --- |
| 任意フォルダのワークスペース作成 | `herdr workspace create --cwd <path> --label <名前> --focus` |
| 一覧を見る | `herdr workspace list` |
| 閉じる | `herdr workspace close <workspace_id>` |

`--label` は省略でき、その場合はフォルダ名から自動で付きます。 `--focus` を付けると作成と同時にそのワークスペースへ移動します。 `workspace_id` は `herdr workspace list` で確認できます。

なお、 `herdr workspace list` や後述の `herdr pane list` など、 `list` 系のコマンドは結果を JSON で返します。そのままだと読みにくいので、 `jq` を通して必要な項目だけ取り出すと見やすくなります。

```bash
# ワークスペースの ID とラベルだけを一覧する
herdr workspace list | jq -r '.result.workspaces[] | "\(.workspace_id)\t\(.label)"'
```

ショートカットからも作れます。

| 操作 | キー |
| --- | --- |
| 新しいワークスペースを作成 | `prefix+shift+n` |
| 新しい worktree を作成 | `prefix+shift+g` |

ショートカットで作る場合、開始フォルダは config.toml の `new_cwd` ポリシーで決まります。 既定は `"follow"` （今のワークスペースを引き継ぐ）で、ほかに `"home"` や固定パスも指定できます。 任意のフォルダを都度指定したいときは CLI の `--cwd` 、決まった場所へサッと足したいときはショートカット、と使い分けられます。

画面内のキー操作に加えて、ターミナルから叩く基本コマンドも押さえておくとよいです。

| コマンド | 説明 |
| --- | --- |
| `herdr` | 起動 / 既存セッションに再接続 |
| `herdr status` | クライアント・サーバーの状態を表示 |
| `herdr server reload-config` | 設定を再読み込み |
| `herdr server stop` | サーバーを停止（動作中のエージェントも終了） |
| `herdr update` | 最新版に更新 |

クライアント（画面）を閉じるだけでは、サーバーとエージェントは動き続けます。サーバーごと止めたいときは `herdr server stop` を使います。

### サイドバーで状態を把握する

herdr ならではの機能が左のサイドバーです。各ペインでエージェントを起動すると、その状態（working / idle / blocked / done）が自動で検出され、サイドバーに色分けで一覧されます。どのエージェントが手を止めているかが一目で分かります。

4 つの状態の意味は次のとおりです。

| 状態 | 意味 |
| --- | --- |
| `working` | 処理を進めている |
| `blocked` | 承認や質問の UI が出ていて、人の応答を待っている |
| `idle` | 入力待ち。そのタブは表示済みで、こちらが状況を把握できている |
| `done` | 入力待ち。ただし作業の完了をまだ見ていない（タブを表示すると idle になる） |

`idle` と `done` は、エージェントの側から見ればどちらも同じ「入力待ち」です。違うのは、その完了を **自分がまだ見ていないかどうか** だけです。バックグラウンドで終わった作業は `done` になり、そのタブをフォーカスするか `pane focus` / `agent focus` で対象にした時点で `idle` に変わります。裏で走らせていた作業のうち、まだ結果を確認していないものだけが `done` として残る仕組みです。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/t/techscore/20260731/20260731140952.png)

### Git worktree を扱う

機能ごとにブランチを分けて並行作業するとき、herdr は Git worktree もワークスペースとして扱えます。作成・切り替え・削除は CLI で操作できます。

| 操作 | コマンド |
| --- | --- |
| 一覧を見る | `herdr worktree list --cwd <repo>` |
| 作成して開く | `herdr worktree create --cwd <repo> --branch feature-a` |
| 既存の worktree に切り替える | `herdr worktree open --cwd <repo> --branch feature-a` |
| 削除する | `herdr worktree remove --workspace <id> --force` |

worktree のチェックアウト先は `~/.herdr/worktrees/<repo>/<branch>` です。 対象リポジトリは `--cwd` で指定します。これを省くと現在のワークスペースが対象になり、Git リポジトリの外だと `not_git_worktree` エラーになります。

コマンドを覚えなくても、サイドバーのワークスペースを右クリックすれば、そのメニューから worktree を作成できます。ふだんは右クリックで手早く済ませ、スクリプトやショートカットに組み込みたいときは CLI を使います。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/t/techscore/20260731/20260731141104.png)

### 名前付きセッションを使い分ける

セッションには名前を付けて複数持てます。 `--session <name>` で起動すると、その名前のセッションを新規作成または再接続します（名前を省くと `default` ）。

| コマンド | 説明 |
| --- | --- |
| `herdr --session <name>` | 名前付きセッションを起動 / 再接続 |
| `herdr session list` | セッション一覧（名前・状態・ディレクトリ） |
| `herdr session attach <name>` | 別のセッションに接続を切り替える |
| `herdr session stop <name>` | セッションごと停止 |
| `herdr session delete <name>` | 停止済みセッションを削除 |

セッションはそれぞれ独立したサーバー（ソケット）です。切り替えは tmux の `attach` と同じで、デタッチ（既定 `prefix+q` ）してから別のセッションへ `attach` し直します。1 セッション内で並行作業を分けるワークスペースとは階層が違います。

---

## 別のエージェントにプロンプトを送信する

複数のエージェントを別々のペインで動かしていると、「片方で分かったことを、もう片方に伝えたい」場面が出てきます。人間がコピペで往復するのは手間です。 `herdr agent prompt` を使うと、別のペインで動いているエージェントにプロンプトを直接送信し、そのまま実行させられます。

送信されたプロンプトはサブミットまで行われます。相手の入力欄に書きかけのテキストが残っていると、それに続けて連結された状態でサブミットされる点は注意してください。入力欄にテキストを置くだけでサブミットしたくない場合は、 `herdr pane send-text` を使います。

送り先（target）はペイン ID です。 `herdr pane list` や `herdr agent list` で確認できます。

```bash
# 送り先のペイン ID を調べる
herdr pane list --workspace <id>

# そのペインのエージェントにプロンプトを送る
herdr agent prompt <pane_id> "<プロンプト本文>"
```

たとえば次のような場面で役立ちます。

**作業を別のエージェントに引き継ぐ** 。あるエージェントで進めていた調査や実装を、別のペインのエージェントに渡して続きを任せます。

```bash
herdr agent prompt w32:p1 "認証まわりのリファクタを引き継いでください。ここまでで src/auth/token.ts の検証ロジックを関数に切り出し済みです。残りは呼び出し側 3 箇所の置き換えと、期限切れトークンのテスト追加です。まず変更済みの token.ts を読んでから続きを進めてください。"
```

**worktree で動くエージェントにコンテキストを渡す** 。ローカルで試していた内容を、新しく作った worktree のエージェントに引き継いで本実装させます。

```bash
# 1. 機能用の worktree を作る（--focus で開く）
herdr worktree create --cwd <repo> --branch feature-search --focus

# 2. その worktree のペインでエージェントを起動
herdr agent start claude --kind claude --pane <new_pane_id>

# 3. これまでの経緯と方針をまとめて渡す
herdr agent prompt <new_pane_id> "この worktree で全文検索機能を実装します。ローカルで PoC したところ、pg_trgm より Meilisearch のほうがレスポンスが安定していました。方針は Meilisearch を採用し、articles テーブルを index 化、/search API を新設する形です。まず docs/adr/0007-search.md に方針を書いてから実装に入ってください。"
```

送信後に相手の処理が終わるまで待ちたいときは `--wait` を付けます。 `--until` を省略すれば idle / done / blocked のいずれかで待機を抜けるので、ふだんは省略しておけば十分です。人間がコピペで運ぶ代わりに、コンテキストを渡して処理が終わるまで待つところまでを 1 コマンドで書けます。

### コマンドを覚えなくても、自然な言葉で頼める

`herdr agent prompt` のようなコマンドは、実は自分で打つ必要すらありません。いま作業しているエージェント自身が herdr の CLI を呼べるので、次のように自然な言葉で頼むだけで実行してくれます。

- 「左のペインのエージェントに、この方針を伝えておいて」
- 「隣のペインで調べ物をしている子に、さっき分かった手順を共有して」

エージェントは送り先のペイン ID を `herdr pane list` で調べ、 `herdr agent prompt` を組み立てて送信します。 この記事を書いている最中も、実際に「左のペインの Claude に教えてあげて」と頼んで、herdr-toolkit の構成メモを隣のペインへ送っています。

同じ要領で、プロンプト送信以外のペイン操作も言葉で頼めます。

| 頼みたいこと | 裏で使われる CLI |
| --- | --- |
| 「ペインを分割して別の作業を並べて」 | `herdr pane split` |
| 「そこでエージェントを起動して」 | `herdr agent start` |
| 「あのエージェントが終わるまで待って」 | `herdr agent wait` |
| 「このコマンドを隣のペインで流して」 | `herdr pane run` |
| 「終わったら通知して」 | `herdr notification show` |

CLI を暗記しなくても、やりたいことを言葉で伝えれば、エージェントが herdr の CLI コマンドに変換して実行してくれます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/t/techscore/20260731/20260731141315.png)

---

## herdr の操作をエージェント自身に任せる

複数のエージェントに作業を分担させる段取りは、herdr の CLI を組み合わせてシェルスクリプトで書くこともできます。 ただ、そのスクリプトを書いて保守するのは手間です。herdr の CLI はエージェント自身も叩けるので、段取りそのものを 1 体のエージェントに任せられます。 本記事では、この取りまとめ役を **lead** （リード＝司令塔役）、実作業を分担する側を **helper** （ヘルパー＝手伝い役）と呼びます。lead が helper を起動して指示を出し、結果を集約する、という役割分担です。

これを可能にするのが公式の [Agent skill](https://herdr.dev/docs/agent-skill/) です。 エージェントに読み込ませると、そのエージェントは「ペインを増やす」「そこに別のエージェントを起動する」「終わるまで待つ」といった herdr の操作を、自分の判断で行えるようになります。

導入は 3 ステップです。

```bash
# 1. Agent skill を（グローバルに）インストールする
npx skills add ogulcancelik/herdr --skill herdr -g

# 2. lead を起動するペインの ID を調べる（ID・作業ディレクトリ・タイトルを一覧）
herdr pane list --workspace w1 | jq -r '.result.panes[] | "\(.pane_id)\t\(.foreground_cwd)\t\(.terminal_title_stripped // "-")"'

# 3. skill を読み込んだ lead エージェントを、そのペインで起動する
herdr agent start lead --kind claude --pane w1:p1
```

ペイン ID は `<workspace>:<pane>` という形式（例 `w1:p1` ）で、いま開いているペインの ID もこの一覧に出ます。

ここで大事なのは、 **あなたが用意するのは lead 1 体だけ** という点です。 helper を事前に作ったり起動したりする必要はありません。helper は、lead が指示を受けてから `herdr` の CLI を使って自分で作ります。

あとは lead に、ふだん人にお願いするのと同じ言葉で頼むだけです。たとえば「lint・test・docs 生成をそれぞれ別のエージェントに分担させて、全部終わったら結果をまとめて」と伝えると、lead は次のように動きます。

1. `herdr pane split` でペインを 3 つに増やす
2. 増やした各ペインで `herdr agent start` を呼び、lint 係・test 係・docs 係の helper（=ふつうのエージェント）を起動する
3. それぞれの helper に担当作業を指示する
4. `herdr agent wait` で 3 体の完了を待ち、揃ったところで結果を 1 つにまとめて報告する

つまり helper とは「lead が作業のために立てた、ふつうのエージェント」です。helper 側に特別な準備は要りません。 Agent skill を読ませて `herdr agent start` するのは lead の 1 体だけで、あとの分割・起動・待ち合わせ・集約はすべて lead が代行します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/t/techscore/20260731/20260731141400.png)

実際に動かすと、次のように lead が helper のペインを 3 つ立ち上げ、それぞれに指示を送ります。左上は lead、残りの 3 ペインは lead 自身が起動した helper（A・B・C）です。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/t/techscore/20260731/20260731141446.png)

この段取りは lead が状況を見ながら自分で組み立てて実行します。分担の粒度を変えたいときも、スクリプトを書き直すのではなく lead への指示を変えるだけで済みます。

---

## エージェントの完了を待って次の作業を自動で流す

段取りを自分で組みたいときは、herdr の CLI を直接つないで自動化できます。複数のエージェントを回していると、「実装が終わったか」「テストが通ったか」を自分でペインを見て回り、次の指示を手で出すことになります。ここを自動化するには、次のコマンドを使います。

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

これらをつなぐと、「実装の完了を待つ → テストを起動 → 失敗したら通知」が 1 本のスクリプトになります。

まず、対象のペイン ID を調べて変数に控えます。ペイン ID は `<workspace>:<pane>` の形（例 `w1:p2` ）で、 `herdr pane list` で確認できます。 ここでは実装エージェントが動いているペインを `IMPL` 、テストを流すペインを `TEST` とします。

```bash
# ペイン一覧から ID を確認する（cwd や起動中エージェント名で見分ける）
herdr pane list --workspace w1

# 控えた ID を変数に入れておく
IMPL=w1:p1   # 実装エージェントが動いているペイン
TEST=w1:p2   # テストを流すペイン
```

あとはこの 2 つをつなぐだけです。

```bash
# 実装エージェントの完了を待ってテストを起動し、失敗したら通知する
herdr agent wait "$IMPL"
herdr pane run "$TEST" "sh -c 'npm test && echo DONE'"
herdr pane wait-output "$TEST" --match DONE || herdr notification show "test failed"
```

人間はペインに張り付かず、通知が来たときだけ確認すれば済みます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/t/techscore/20260731/20260731141528.png)

---

## どの端末からでも同じ作業環境に戻る

ターミナルを閉じたり別のマシンに移ったりすると、動かしていたエージェントや作業状態が失われます。

herdr はサーバー常駐型です。クライアントを閉じてもサーバー側でエージェントは動き続け、次の操作でどこからでも元の状態に戻れます。

| やりたいこと | 操作 |
| --- | --- |
| デタッチする（切り離す） | `prefix+q` |
| 再接続する | `herdr` （名前付きは `herdr session attach <name>` ） |
| 別マシンから接続する | `herdr --remote <ssh-target>` |
| 再起動後もエージェントを復元する | config.toml で `resume_agents_on_restore = true` （既定で有効） |

---

## 足りない機能はプラグインで自作する

既存のプラグインで足りないなら、自分で作れます。herdr のプラグインは特別な SDK を必要とせず、 **実行ファイルとマニフェストだけ** でできています。 この章では、まず最小のプラグインを作って仕組みを掴み、続けて実用的な自作例（キー起動型とイベント反応型）を 2 つ見ていきます。

### 最小のプラグインを作ってみる

「今のリポジトリに worktree を切る」だけの最小プラグインを作り、仕組みを掴みます。

**1\. やることをスクリプトにする**

```bash
#!/usr/bin/env bash
# wt.sh — 今のリポジトリに worktree を 1 つ切る
set -eu
# plugin action の $PWD はプラグイン自身の場所。対象リポジトリは
# HERDR_PLUGIN_CONTEXT_JSON（フォーカス中のペインの情報）から取り出す
cwd="$(printf '%s' "$HERDR_PLUGIN_CONTEXT_JSON" | jq -r '.focused_pane_cwd')"
herdr worktree create --cwd "$cwd" --branch "wt/$(date +%m%d-%H%M%S)" --focus
```

**2\. マニフェストを添えてプラグインにする**

```toml
# herdr-plugin.toml（wt.sh と同じフォルダに置く）
id = "me.wt"
name = "wt"
version = "0.1.0"
min_herdr_version = "0.7.5"

[[actions]]
id = "new"
title = "現在のリポジトリに worktree を切る"
command = ["bash", "wt.sh"]
```

この 2 ファイルを 1 フォルダに置いて登録します。

```bash
herdr plugin link ./wt # 開発中はローカルをリンク
herdr plugin list      # 登録できたか確認
```

**3\. ショートカットに割り当てる**

```toml
# ~/.config/herdr/config.toml
[[keys.command]]
key = "prefix+shift+n"
type = "plugin_action"
command = "me.wt.new"
description = "現在のリポジトリに worktree を切る"
```

`herdr server reload-config` で反映されます。これで長い worktree コマンドが **キー 1 つ** になりました。 プラグインの部品は「スクリプト＋マニフェスト（＋任意でキー割当）」の 3 つだけです。GitHub に置けば `herdr plugin install <owner>/<repo>` で誰でも入れられます。

### 実例: ディレクトリを選んで workspace を作る

基本操作の節で触れたとおり、任意フォルダ起点のワークスペース作成は CLI の `herdr workspace create --cwd` でしかできません。 キーからは `new_cwd` ポリシーに縛られ、毎回違うフォルダを選べません。 この穴を埋めるプラグインを実際に作りました（ [open-dir](https://github.com/s-hiraoku/herdr-toolkit/tree/main/plugins/open-dir) ）。 「キーを押す → ディレクトリを選ぶ → その場所に workspace を作る」を 1 アクションにします。

コアは短く、やっていることは 2 つだけです。候補のディレクトリを集めて選ばせ、選んだパスを `herdr workspace create --cwd` に渡します。

```bash
# 候補を集める（zoxide → ghq → 走査 の順で、あるものを使う）
gather_candidates() {
  command -v zoxide >/dev/null && { zoxide query -l; return; }
  command -v ghq    >/dev/null && { ghq list --full-path; return; }
  find "$HOME/ghq" -mindepth 1 -maxdepth 2 -type d
}

# 選ばせて、そのフォルダを起点に workspace を作る
dir="$(gather_candidates | fzf)"                       # fzf が無ければ純 bash の番号選択にフォールバック
herdr workspace create --cwd "$dir" --label "$(basename "$dir")" --focus
```

ポイントは、 `zoxide` や `ghq` といった **ユーザーが既に使っている仕組みをそのまま候補の取得元にする** ことです。どちらも無ければ設定したルートを浅く走査し、 `fzf` が無ければ純 bash の番号選択にフォールバックするので、外部依存ゼロでも動きます。

対話 picker を出すため、キーは `plugin_action` （非対話）ではなく `popup` （TTY 付きのフローティングターミナル）で割り当てます。

```toml
# ~/.config/herdr/config.toml
[[keys.command]]
key = "prefix+shift+o"
type = "popup"
command = '''bash "$HOME/ghq/github.com/s-hiraoku/herdr-toolkit/plugins/open-dir/open-dir.sh"'''
description = "open-dir: ディレクトリを選んで workspace 作成"
```

本体機能の穴も、埋めるのは数十行のシェルスクリプトで済みます。これが「ユーザーが自分で道具を足せる」という herdr の強みです。

### 実例: エージェントの状態変化に自動で反応させる

ここまでのプラグインはキーやコマンドで「こちらから呼ぶ」ものでした。herdr はイベントを購読して「向こうから反応する」プラグインも書けます。

たとえば別のウィンドウで作業していると、エージェントが入力待ちになっても気づけません。 `pane.agent_status_changed` イベントを使えば、入力待ちや完了になった瞬間、そのペインへ自動でフォーカスを移せます。 マニフェストでは `[[actions]]` の代わりに `[[events]]` で購読するイベントを宣言します。

```toml
# herdr-plugin.toml
id = "me.autofocus"
name = "autofocus"
version = "0.1.0"
min_herdr_version = "0.7.5"

[[events]]
on = "pane.agent_status_changed"
command = ["bash", "autofocus.sh"]
```
```bash
#!/usr/bin/env bash
# autofocus.sh — 入力待ち/完了になったペインへフォーカスを移す
set -eu
data="${HERDR_PLUGIN_EVENT_JSON:-}" # イベント内容が JSON で渡される
status="$(printf '%s' "$data" | jq -r '.data.agent_status // empty')"
ws="$(printf '%s' "$data" | jq -r '.data.workspace_id // empty')"
pane="$(printf '%s' "$data" | jq -r '.data.pane_id // empty')"
# 「あなたの操作が要る」状態だけに絞る（working のたびに奪うと邪魔なので）
case "$status" in
  blocked | done)
    herdr workspace focus "$ws"
    herdr agent focus "$pane"
    ;;
esac
```

イベントフックには `HERDR_PLUGIN_EVENT_JSON` で内容（ `agent_status` ・ `workspace_id` ・ `pane_id` など）が渡されます。これで「別の作業をしながら、呼ばれたときだけ herdr に戻る」といった運用もできます。

---

## herdr をもっと便利に使うために — 相性のいいツール

herdr の機能そのものではありませんが、ペインに常駐させて組み合わせると便利なツールもあります。

たとえば差分レビューです。差分を見るだけなら difit や lazygit が定番ですが、 ここでは保存のたびに自動で追従する [hunk](https://github.com/modem-dev/hunk) を挙げます。 ペインに常駐させておくと、エージェントが書き換えるたびに差分が更新され、レビューがターミナル内で完結します。

```bash
# npm パッケージ名は hunkdiff、コマンドは hunk
npm i -g hunkdiff

# 作業ツリーの差分を開き、変更を自動で追う
hunk diff --watch
```

![](https://cdn-ak.f.st-hatena.com/images/fotolife/t/techscore/20260731/20260731141620.png)

差分に行コメントを付けてそのままエージェントへ送り返したいなら、herdr プラグインの [herdr-reviewr](https://github.com/persiyanov/herdr-reviewr) もあります。 導入は `herdr plugin install persiyanov/herdr-reviewr` です。 いずれも herdr 本体の機能ではないので、必要になったら試す、くらいの位置づけです。

herdr 自体が AI エージェントを動かすために作られたツールです。hunk のように AI と組み合わせて活きるツールを一緒に使えば、AI と開発する毎日がさらに快適になるはずです。

---

## まとめ

エージェントへのプロンプト送信、段取りごと任せるエージェント連携、完了待ちの自動化、セッションの永続化。 herdr があれば、複数の AI エージェントを使う開発をターミナルの中で回せます。そのうえ、足りない機能は自作プラグインで足せます。 herdr は「与えられた機能を使うツール」ではなく、「自分で拡張していくツール」です。

---

### 参考リンク

- [herdr 公式ドキュメント](https://herdr.dev/docs/)
- [エージェントの自動化（状態の定義）](https://herdr.dev/ja/docs/agent-automation/)
- [Socket API](https://herdr.dev/docs/socket-api/)
- [Configuration](https://herdr.dev/docs/configuration/)
- [Plugins](https://herdr.dev/docs/plugins/)
- [herdr(GitHub リポジトリ)](https://github.com/ogulcancelik/herdr)

[シナジーマーケティング株式会社では一緒に働く仲間を募集しています。](https://synergist.jp/forengineer/)