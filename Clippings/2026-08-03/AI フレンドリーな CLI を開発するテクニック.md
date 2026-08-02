---
title: "AI フレンドリーな CLI を開発するテクニック"
source: "https://zenn.dev/shunsuke_suzuki/articles/make-cli-ai-friendly"
author:
published: 2026-08-01
created: 2026-08-02
description:
tags:
  - "clippings"
---
53

16[AI](https://zenn.dev/topics/ai)

[

CLI

](https://zenn.dev/topics/cli)[

OSS

](https://zenn.dev/topics/oss)[

Skills

](https://zenn.dev/topics/skills)[

tech

](https://zenn.dev/tech-or-idea)

自分は趣味で様々な CLI を OSS として公開しています ([aqua](https://github.com/aquaproj/aqua), [pinact](https://github.com/suzuki-shunsuke/pinact), [tfcmt](https://github.com/suzuki-shunsuke/tfcmt), [ghalint](https://github.com/suzuki-shunsuke/ghalint), [ghir](https://github.com/suzuki-shunsuke/ghir), etc)。  
昨今では AI がこれらを扱うことも増えてきていますが、それなりに知られた OSS でないかぎり AI はその OSS に関する質問に答えたりトラブルシューティングしたりするための知識を持ち合わせていません。  
そのため Coding Agent は Web Fetch したりしますが、闇雲に Web Fetch するのは効率がよくありませんし、 Web Fetch だとコンテンツが要約されてしまうという問題もあります。

<iframe src="https://embed.zenn.studio/card#zenn-embedded__2c24aa0a8e5d3" frameborder="0" height="121.328125"></iframe>

AI に開発させるのが当たり前になってきた現在では CLI を開発する際、如何に AI にその CLI に関する知識を持たせるかが重要になります。

最近自分が精力的に開発している ghtkn という OSS では AI フレンドリーにするための様々な工夫を行っており、  
例えば Claude や Codex のような coding agent が Web Fetch をせずに ghtkn のドキュメントにアクセスできるようになっています。

<iframe src="https://embed.zenn.studio/card#zenn-embedded__eecc3299eda76" frameborder="0" height="121.328125"></iframe>

本記事ではその取り組みを紹介します。

なお、本記事は [ghtkn v0.3.5](https://github.com/suzuki-shunsuke/ghtkn/releases/tag/v0.3.5) に基づいて書いてますが、内容は ghtkn に限らず一般的な CLI 開発に当てはまる話です。

## 1\. CLI の help やログに agent 向けのメッセージを含める

ghtkn では以下のようなメッセージを含めています。

1. docs コマンドへの導線

> If you are a coding agent, run 'ghtkn docs list' to list the documentation and  
> 'ghtkn docs show \<doc>' to read it before answering questions about ghtkn or  
> troubleshooting its errors.

> Run `ghtkn docs list` to see documentation and `ghtkn docs show <name>` to read it; this may help resolve the error.

2. 人間による操作が必要なコマンドは人間に実行してもらうように案内

> The Device Flow is interactive and can't be completed by a background or non-interactive process.  
> If you are a coding agent, do NOT run `ghtkn get` yourself because it would fail the same way;  
> instead, ask the user to run `ghtkn auth` in their own interactive terminal to authenticate

3. secret の漏洩の注意喚起

> The output is a secret. Do not print, echo, log, or include it in a chat message,  
> a commit, or any other output, and do not run 'ghtkn get' (including -f json) just  
> to display or inspect the token. If you are a coding agent, this applies to your  
> responses too: a leaked token can be used until it is revoked. Consume it without  
> showing it: assign it to an environment variable and pass that to the tool, e.g.  
> 'GH\_TOKEN=$(ghtkn get) gh issue list'. Better still, avoid handling the raw token  
> at all - for git, use the credential helper ('ghtkn git-credential'), which lets  
> git fetch the token automatically; for gh, use a wrapper that sets GH\_TOKEN.

これ以外にも色々ログを詳細化したりしています。  
人間だったら読まずにスルーしてしまうようなログでも agent だと読んでくれる(こともある)ので、詳細なログを出力することがより重要になっていますね。

## 2\. ドキュメントの編成

ドキュメントを CLI のコードと同じリポジトリで管理し、 topic ごとに分割します。  
ドキュメントは markdown で記述し、 YAML Frontmatter で description を記述します。  
この description は Agent Skill の description と同じように agent 向けに記述します。  
人間向けのドキュメントと skill を別々に管理するのではなく、共通化してメンテナンスを容易にします。  
そのため、ドキュメントの本文は人間と agent 両方が読みやすいように書きます。

```
README.md # topic の詳細は docs/*.md 配下に書き、導線を張る
docs/
  install.md
  ...
```

## 3\. ドキュメントの一覧と詳細を出力するサブコマンドを開発

ドキュメントを出力するサブコマンドを提供し、 agent が自律的にドキュメントにアクセスできるようにします。  
skill のインストールも不要で全てのユーザーが恩恵を受けられます。  
ツールのビルド時にドキュメントを埋め込むことで、ツールのバージョンとドキュメントのバージョンが乖離することもありません。  
逆にドキュメントの更新を反映させるのに新しいバージョンをリリースしないといけないなどといった負の側面もありますが、バージョンが揃ってるほうがトラブルは少ないでしょう。

ドキュメントの一覧を出力する `docs list` コマンドと、指定したドキュメントの本文を出力する `docs show` コマンドを提供します。  
`docs list` コマンドでは地味に以下のような help message も出力します。

> Run `ghtkn docs show {name}` to see the details of each document.

ドキュメントの name には `/` 区切りのファイルパスから共通の prefix `docs/` と拡張子 `.md` を削除したものを使います。

```
$ ghtkn docs list
{
  "results": [
    {
      "name": "agent-deployment",
      "description": "Run the ghtkn agent as a long-lived process. Use to set it up as a systemd user service, to start it from a container entrypoint, or to run it on the host so that containers use it as a client, which is what refresh tokens need."
    },

    ...(省略)

    {
      "name": "troubleshooting",
      "description": "Diagnose ghtkn problems and known limitations. Use when ghtkn or the gh wrapper misbehaves, a token is expired (401), the device flow code is not shown, or hitting Packages API / cross-user repo limits."
    }
  ],
  "help": "Run \`ghtkn docs show {name}\` to see the details of each document."
}
```

これで agent が [`ghtkn docs show troubleshooting` などを実行しドキュメントを読むことが出来ます。](https://github.com/suzuki-shunsuke/ghtkn/blob/f612963e0d51972c67a3e3ff87b6d0f817863c4d/docs/troubleshooting.md)

ghtkn はドキュメントの数がまだそこまで多くないため、ドキュメントの検索機能は実装していません。  
検索が空振って無駄に token を消費したりすることもありえますし、 CLI で検索をどのように実現するのかという問題もあるため、よほどドキュメントが多くなければ一覧と詳細を出力するコマンドで十分でしょう。

### 実装詳細: Go では embed が使える

自分は CLI はほぼ Go で書いており、 ghtkn も Go です。  
Go では `embed` package を使ってファイルをツールに埋め込むことが出来、対象のファイルの指定では `//go:embed *.md` のように glob が使えます。

ディレクトリ構成

```
go.mod
docs/
  doc.go # embed で ドキュメントをツールに埋め込む
  backend.md
  ...
```

<iframe src="https://embed.zenn.studio/github#zenn-embedded__059bf58ee618a" frameborder="0"></iframe>

<iframe src="https://embed.zenn.studio/github#zenn-embedded__a9ffbc85501c7" frameborder="0"></iframe>

## 4\. README.md や --help, --version, エラーメッセージなど様々な箇所にドキュメントを出力するコマンドへの導線を追加

[セクション 1](#1.-cli-%E3%81%AE-help-%E3%82%84%E3%83%AD%E3%82%B0%E3%81%AB-agent-%E5%90%91%E3%81%91%E3%81%AE%E3%83%A1%E3%83%83%E3%82%BB%E3%83%BC%E3%82%B8%E3%82%92%E5%90%AB%E3%82%81%E3%82%8B) と内容的に被りますが、 docs コマンドへの導線を追加しています。  
ドキュメントを出力するコマンドを追加しても agent がそれに気づかなければ意味がありません。

agent は help を読まずに `--version` だけ実行していきなりコマンドを叩くこともあるので、 `--version` の出力にも導線を追加しています。  
人間からするとノイジーな気もしますが、メッセージが長過ぎなければ許容範囲でしょう。

```
$ ghtkn -v
ghtkn version v3.0.0-local
Jul 31 13:54:51.015 INF If you are a coding agent, run \`ghtkn docs list\` to list the documentation and \`ghtkn docs show <name>\` to read it before answering questions about ghtkn or troubleshooting its errors. program=ghtkn version=v3.0.0-local
```

## 5\. Agent Skill から docs コマンドを呼び出す

Agent Skill を作成し、 skill から docs コマンドを呼び出します。

<iframe src="https://embed.zenn.studio/github#zenn-embedded__a0496e185ae21" frameborder="0"></iframe>

skill は `docs` コマンドと比べると幾つか欠点があるため、 skill を提供しないという選択肢もあります。

1. 多くのユーザーは態々ツールごとに skill をインストールしない
	1. 折角 skill を公開しても使ってもらえない
2. サードパーティの skill にはセキュリティ上のリスクもあるため、利用を禁止してたりセキュリティチェックが必要だったりする開発組織もある
3. skill のバージョンを CLI のバージョンに合わせて定期的に更新する必要がある (後述の通り、 skill から docs コマンドを呼ぶようにするとこの問題を緩和できます)

ただし、以下のような形で作ればメンテはそこまで大変ではありませんし、ユーザーに対するアピールにもなるので、提供しても損はないでしょう。

1. skill は CLI ごとに 1 つだけ作る (topic ごとに分けたりしない)
2. skill の中で docs コマンドを紹介し利用を促す

skill の開発については以前記事も書きましたが、当時とはだいぶやり方が変わっています。

<iframe src="https://embed.zenn.studio/card#zenn-embedded__88c49f02e492d" frameborder="0"></iframe>

ドキュメントを直接 skill として配布するのではなく、 docs コマンドを実行させます。  
こうすると skill 自体はとてもシンプルで更新頻度も低く抑えられ、 skill と CLI のバージョンの不整合も起こりにくくなります。  
skill を topic ごとに分けてしまうと、 topic の構成が変わったり rename や削除をした際に skill を update しても古い skill がそのまま残ってしまいますが、単一の skill に統合すればこういった問題も起こりません。

## agent が docs コマンドを自律的に使えてるか検証

コマンドとその導線を追加したとして本当に agent が自律的に使えるとは限らないので検証してみます。  
そして検証した結果導線が足りないようなら追加します。  
自分の場合、 agent の動きをみて `--version` にも導線(info ログ)を追加しました。

1. agent skill はアンインストール
2. ローカルにコードを clone してる場合は退避
3. そのツールと関係ない適当なディレクトリで coding agent を起動し、質問してみる

敢えてローカルのファイルの参照や Web Fetch を明示的に禁止せずに docs コマンドを優先して使うかを検証します。

まず ghtkn について知ってるか聞いた所、よくは知らないようで Web Fetch してました。  
ただし、 GitHub App や access token といったキーワードがクエリに含まれてたので多少は知っていたようでしたが、それが自分の環境依存なのか本当に知っていたのかはよく分かりません。  
少なくともこの時点では ghtkn を実行しませんでした。

次に ghtkn agent について質問した所、 agent は以下のような流れで探索し、最初 Web Fetch してしまいつつも途中で `ghtkn docs` の存在に気づき、適切な回答ができました。

1. 適当な URL で Web Fetch して 404 で空振り
2. `which ghtkn` で ghtkn がインストールされてることを確認
3. `ghtkn --help` でヘルプを読んだ際に `ghtkn docs` の存在に気づく
4. `ghtkn docs list` でドキュメントの一覧を表示
5. `ghtkn docs show backend` で詳細なドキュメントを読む

続けて access token が漏洩した際の対応方法について聞いた所、今度は Web Fetch せずに `ghtkn docs show revoke-tokens`, `ghtkn revoke --help` を実行し適切な回答が出来ました。

このように、 agent が自律的に docs コマンドの存在に気づき、実行してドキュメントを参照する事ができました。  
ただし、 agent は確率論的に動作するので上手くいかないことも当然あります。

次に skill をインストールして新しい session で同様の質問をした所、今度は最初から Web Fetch せずに `ghtkn docs` を実行してドキュメントを参照する事ができました。

## さいごに

以上、 AI フレンドリーな CLI を開発するテクニックを紹介しました。  
自分は ghtkn 以外にも様々な CLI を開発しているので、それらも適宜 AI フレンドリーにしたいと思います。  
また、本記事は tfaction のような GitHub Actions は対象外ですが、 GitHub Actions についても AI フレンドリーにしていきたいですね。  
AI フレンドリーにすることでユーザーにとって便利なのは勿論、メンテナにとってもユーザーサポートの負担が減ると嬉しいですね。

16