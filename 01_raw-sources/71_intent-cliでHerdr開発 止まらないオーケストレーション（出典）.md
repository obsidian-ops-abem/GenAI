---
title: "71_intent-cliでHerdr開発 止まらないオーケストレーション（出典）"
tags: [raw-source]
source: https://zenn.dev/jtechjapan_pub/articles/intent-cli-herdr-orchestration
author: 高丘 @tomohisa（株式会社ジェイテックジャパン）
published: 2026-08-03
created: 2026-08-03
---

# 出典メタデータ

- URL: https://zenn.dev/jtechjapan_pub/articles/intent-cli-herdr-orchestration
- 著者: 高丘 @tomohisa（株式会社ジェイテックジャパン CTO）
- 公開: 2026年8月3日
- 関連リポジトリ: intent-system (Apache-2.0)、agmsg (fujibee/agmsg)、herdr
- シリーズ: コンセプト編 / 実践編 / Loop Engineering編 の続編

---

# 本文

株式会社ジェイテックジャパン CTOの [高丘 @tomohisa](https://twitter.com/tomohisa) です。

AIエージェントを4体、止まりを見つけて再開させながら回して開発を進める、ということを日常的にやっています。設計・オーケストレーション・実装・レビューの4スレッドが、GitHub の Issue と PR を通じて協調する仕組みです。この土台に **Intent CLI** （プロジェクト名 intent-system）という OSS を使っています。

## まず前提: 4スレッドと「設計スレッドだけと話す」

4つのスレッド（＝コーディングエージェントのセッション）で、指示の流れはこうです。

- **設計スレッド** — 意図をまとめ、判断（ADR）を残し、作業単位（packet）と受け入れ基準を決める。
- **オーケストレーションスレッド** — 準備できた packet を GitHub の Issue として1回に1件だけ切り出し、実装・レビューへ渡し、CI やレビューの完了を待ち、マージと締め（closeout）を進める。判断はせず、進行役に徹する。
- **実装スレッド** — Issue を受けて実装し、PR を作る。見るのは GitHub の Issue/PR とコードだけ。
- **レビュースレッド** — PR を、元の Issue・packet・意図・ADR と突き合わせて確認する。

通常運用では、人間の入口を **設計スレッドだけ** に限定しています。人間まで上がってくるのは、意図の曖昧さ、受け入れ基準の欠落、セキュリティや破壊的操作といった「人間が決めるしかないこと」だけです。

規模：直近およそ4か月で作業単位が1,537件（うち完了1,531件）、マージした PR がおよそ1,460件（2026年8月時点）。

## なぜ「turn を起こすメッセージ」が要るのか

コーディングエージェントのセッションは、 **turn（発話）が来て初めて動く** ものです。

agmsg（エージェント間メッセージング）の monitor が、 **受信メッセージを会話の中の turn に変換** してくれていました。この設計から学んだ2つ：

1. **黙って完了してはいけない** — 完了報告のない作業は「失われた仕事」になる。実測で88分放置された例あり。
2. **publish と委譲は同じ turn の中でやりきる** — 後回しにするとメッセージ駆動では次の turn が作れない。あるドメインで合計およそ60時間の停止を招いた例あり。

## herdr で「任意のスレッドを起こす」

agmsg から herdr に寄せた中心理由：**受信側の常駐という故障点を減らしたい**。

agmsg の codex bridge はコードの約27%（約2,900行）を占め、受信側に monitor や bridge が常駐していないとメッセージが配送されない。herdr は `herdr agent prompt <ペイン>` で任意のペインにテキストを送り込めるため、**どのスレッドの turn でも観測する側から起こせる**。

agmsg を「やめた」わけではなく、agmsg モード（既定/PRIMARY）と herdr-only モード（プレビュー）の2つを選択可能。1チームにつき1モードで混在は不可。

## agmsg を外したら、誰も次を起こさなくなった

herdr-only の最初の実仕事で、3つのロールが全員 idle のまま停止。agmsg の完了報告経路が消え、完了が自分のペインにしか現れなくなった。

直し方：**wake（起こす）経路を1本に頼らず、冗長にする**。3つの経路を重ねています：

1. **一次: 完了報告で起こす** — worker が `intent-cli notify report` でオーケストレーションのペインを prompt して起こす。
2. **二次: 状態変化で起こす** — herdr の socket API `events.subscribe` で `pane.agent_status_changed` を監視。`working` から settled（`idle`/`done`/`blocked`）へ移った瞬間だけ起こす。
3. **最後の網: 定期点検で拾う** — `intent-cli automation stalled-work` で「止まっている作業」を洗い出し。

基準：**「どの単一の経路が落ちても、停止は検出できる状態を保つ」**。

重要な原則：**状態が変わったことは、作業が成功したことを意味しない**。ペインが idle になっても承認待ちかもしれない。成果物と外部の事実（intent-cli / GitHub）で確かめる。

## 止まる原因の多くは「承認プロンプト」

権限の承認プロンプト（`rm -rf /tmp/...`、PR 作成、PR コメント追加等）で頻発停止。

方針：**オーケストレーションスレッドは承認を勝手に押さない**。認証・セキュリティ・権限・破壊的操作・設計判断は常に人間（設計スレッド経由）にエスカレーション。「セッションを進めることと、セッションの代わりに判断することは、別のこと」。

対策：任意の `/tmp` を消させるのをやめ、git 管理から外した専用 worktree 置き場（`.intent-cli/worktrees/`）だけを対象に。

## 複数ロール・複数ドメインを衝突させない工夫

- **ロールごとに作業場所を分ける** — 実装とレビューは独立クローンか worktree に分ける。フォルダ共有すると片方が静かに受信を止めることがある。
- **メタデータとコードのブランチを分ける** — 意図や作業状態は `main-metadata`、実装コードは `main`。
- **実装は GitHub の契約だけを見る** — ホスト側のメタデータを読み書きしない。
- **WIP を1件に絞る** — 未完了作業がある間は新しい Issue を切り出さない。
- **`git add -A` を使わない** — 該当ファイルだけ add。push 直前に `git pull --ff-only`。
- **アイドル時に push しない** — 空 wake が空コミットを積まないよう「何もなければ push しない」を起動プロンプトに必須。

事故から学んだ追加対策：
- **共有プロセスの所有境界** — 消す前に「その資源が自チームのものか」確認。
- **起動報告の検証** — **自己申告は生きている証明にならない**。起動報告後に実際に応答するか確認。

## サブエージェントを「設計スレッドに頼むだけ」で立てる

設計スレッドへの依頼はこれくらい短い：

```
intent-cli の最新の機能を使って、herdr-only のチームを作ってください。
ここを設計、
orchestrator  codex sol high
implement     codex sol middle
reviewer      claude opus 5
で構成してください。
```

裏側のコマンド：

```bash
# 1) 独立したワークスペースを作る
herdr workspace create --label tomohisa_zenn --cwd "$HOME/dev/GitHub/zenn_tomohisa" --no-focus
# => "pane_id":"wP:p1", "workspace_id":"wP" が返る

# 2) そのペインに codex を起動する
herdr agent start zenn-review --kind codex --pane wP:p1 --timeout 180000

# 3) プロンプトを送り、idle/done になるまで待ち、出力を読む
herdr agent prompt wP:p1 "この草稿を査読して…"
herdr agent wait   wP:p1 --until idle --until done --timeout 300000
herdr agent read   wP:p1 --source recent
```

## 実際の規模

4ドメインを1台で並行（intent-cli本体・sekiban-as-a-service・sekiban-wasm-runtime・robocon）。直近1年の GitHub contribution が2万超（オーケストレーションのループが積み上げたコミット・PR含む）。

ループ間隔：メッセージ駆動が主、補う「見張り」として設計スレッド側で30分級リマインダ。Claude Code は `/loop 3m`（プロンプトキャッシュ5分切れる前）、別マシン/クラウドは5分間隔。

## 自分で組むなら / intent-cli なら

要点：
- コーディングエージェントは turn が来ないと動かない。だから「誰が turn を起こすか」を設計する。
- 起こす経路は1本に頼らず、冗長にする（完了報告・状態変化の購読・定期点検）。
- 「状態が変わった」を「成功した」と混同しない。成果物と外部の事実で確かめる。
- ロールごとに worktree とフォルダを分け、メタデータとコードのブランチも分ける。
- 承認・判断は人間に返し、それ以外は自動で回す。

役割分担：**herdr** がペインへの prompt 送信と状態観測を担う。**intent-cli** は宛先のロール対応・委譲と報告の形式・モード記録・止まりの点検・設計境界のイベント記録といった「決めごと」を生成・検証する。intent-cli 自身は AI を起動しない。

### インストール

```
# 前提: .NET 10 SDK
dotnet tool install -g JTechJapan.IntentSystem.Cli
intent-cli --version
```

## まとめ

難しいのは、速く回るチームが止まったときに、それを見つけて再開させ続けること。「誰が turn を起こすか」を作り直し、起こす経路を3段に冗長化した。依頼を1つ減らしたことで土台が単純になり、マルチマシン対応にも進みやすくなった（マルチマシンはまだ将来の課題）。
