---
title: "10_Claude Code共同製作者 Light Cone対談"
tags: [summary, ai, claude-code, anthropic, terminal, claudemd, plan-mode, subagents, wait-and-demand]
source: Light Cone ポッドキャスト（Y Combinator）
author: Claude Code 共同製作者 × Light Cone ホスト陣
published: 2026-08-04
created: 2026-08-04
---

# Claude Code 共同製作者 — Light Cone 対談

> **「今日のモデルでなく6ヶ月先のモデルにビルドする」。Claude Code の誕生（Anthropic Labs・プロトタイプを2日でチームに見せたら翌日には Robert が使っていた・Dario が『ダウンロード数が垂直だが強制してるのか？』と驚く）・CLAUDE.md は wait-and-demand で生まれた・ターミナル形式は最初の選択が予想外に長命・plan mode は『please don't code』を1文足すだけ・dropbox でエンジニア生産性150%向上・サブエージェントスワームで plugin 機能を週末に構築。**

出典: [[88_Claude Code共同製作者 Light Cone対談（出典）]]（Light Cone, 2026-08-04。本文はユーザー提供の文字起こし全文を使用・ASR で"quad code"=Claude Code/"wait and demand"=wait-and-demand 等の誤変換）

---

## 一行で

Y Combinator の Light Cone ポッドキャストが Claude Code 共同製作者（Boris とは別人物・Anthropic Labs チーム出身）を迎えた回。Claude Code の誕生秘話・CLAUDE.md の wait-and-demand・plan mode の正体・subagent スワーム・「6ヶ月先にビルド」哲学。[[07_Boris対談]] が外部向けなら、本対談は YC の創業者向けでより率直。

## Claude Code 誕生（核心）

- **Anthropic Labs チーム**が Claude code/MCP/desktop の3製品を生んだ
- 誰かに頼まれたのでなく「モデルが準備できているのに誰もコーディング製品を作っていない（product overhang）」を察知
- Anthropic AI（Python SDK）を TypeScript に移植してハック開始
- **最初の驚き**：Sonnet 3.5 に「私の聴いている音楽は？」と聞いたら AppleScript を書いて Mac の音楽プレーヤーを調べた。「モデルが思ったより賢い」
- **2日後のプロトタイプ**をチームに見せたら、翌日 Robert（向かいの席）が既に使っていた。Dario が内部ローンチレビューで「ダウンロード数が垂直だが、エンジニアに強制しているのか？」と驚く

## 「6ヶ月先にビルドする」（核心哲学）

> **「今日のモデルでなく、6ヶ月先のモデルにビルドする。モデルが苦手なことを探せ、それは得意になるから」**

scaffolding で10-20%性能向上しても次モデルで無料で得られる。だから**最小限の scaffold で次モデルを待つ**。ターミナル形式が存続したのも「6ヶ月後に有効な UI が思いつかなかった」から。

## CLAUDE.md の誕生（wait-and-demand）

- ユーザーが自分用の markdown ファイルを書き、モデルに読ませ始めた
- **wait-and-demand** で生まれた機能（Claude code 全体も初期 CI 以外はすべて wait-and-demand）
- CLAUDE.md は**短い方が良い**（数千トークン）。モデルが軌道を外れたら少しずつ足す
- モデルが良くなる毎に必要な指示が減る

## ターミナル形式の長寿（本人も驚き）

> **「未だにターミナルを使っているのが信じられない。それは出発点のはずだった」**

- ターミナルの設計は困難（80-100文字・マウスなし・ANSIコード・仮想スクロール必須）
- スピナー1つで50-100回の反復
- Claude code でプロトタイプを量産できる（20個/数時間 vs 従来2週間）

### verbose 論争
- batch 出力を要約に変えたらチームが反乱（Kubernetes ジョブ等で生データが必要）
- file read/search も要約版を出したら不評→verbose mode 追加

## plan mode の正体

> **「plan mode に大きな秘密はない。prompt に『please don't code』を1文足すだけ」**

- ユーザーが「アイデアを練ってコード書かないで」と頼む行動から生まれた（wait-and-demand）
- **plan mode の寿命は限られる**：モデルが自分で plan mode に入れるようになったら不要
- 「次は Claude が直接ユーザーに話す」

## subagent スワーム（plugin 機能の実例）

- エンジニアが spec を Claude に渡し「transformer を使え」と指示
- Claude がチケットを切り・複数 agent を起動・タスクを拾わせ・彼此調整
- **人間の介入ほぼなしで週末数日で plugin 機能を完成**
- 現在の agent の大半は Claude が subagent として起動したもの

## 生産性データ

- **Dropbox**：エンジニア生産性（単純な workloads 測定）150% 向上（Claude code 導入以来）。コミット寿命等でも交差検証
- 従来2%向上でも数百人が1年かかった仕事
- Anthropic 内部：全技術職が毎日 Claude code 使用、営業チームの半数も

## 採用と「間違いを認められるか」

- 「間違った例を1つ挙げて」が採用で有用（間違いを認め・学んだか）
- 「Claude code の transcript を採用材料にするか？→ なる。plan mode を使うか/軌道修正できるか/systems を理解しているかが見える。スパイダーウェブグラフで skill 判定を想像できる」

## DevTool 創業者への助言

> **「モデルがやりたいことを探し、それを容易にしろ。箱に入れて『これで世界と相互作用しろ』ではダメ。ユーザーと同じように、モデルの潜在需要を満たせ」**

## Claude teams / agentology

- 複数の agent が独立した context 窗を持ち、適切な topology で通信→より大きなものを構築
- Claude teams はその一形態。今後さらに来る
- 「Claude 自身が Slack でユーザーに返答・GitHub issue に返答・時々ツイート」

## 関連

- [[07_Boris対談 Anthropic内部Claude運用とエージェント設計]] — 同じ Claude Code チームの Boris。本対談はその誕生側の生の声
- [[09_ジェネレータ×エバリュエータハーネスで長時間走るエージェント]] — 同 Anthropic の Ash/Andrew によるハーネス設計。本対談の「scaffolding は次モデルで無料に」を実践する側
- [[01_Claude×Obsidianで第二の脳を作る]] / [[02_Claude Code×Obsidianで第二の脳を作る 2000時間の設計ルール]] — CLAUDE.md 設計の実践。本対談はその CLAUDE.md の起源（wait-and-demand）を語る
- [[04_Stop Vibe Coding Spec駆動開発の5ブロック]] — spec 駆動。本対談の「plan mode = please don't code の1文」は spec の軽量版
- [[06_5層モデル各層の作業単位 プロンプトからグラフへ]] — 抽象化レベル上昇。本対談の「software engineer の称号が消え builder/PM になる」予測

## 所感

「今日でなく6ヶ月先にビルド」は、[[09_ジェネレータ×エバリュエータ]] の「frontier は移動する・harness はモデル進化で簡素化」と同じ哲学の製品側から見た版。CLAUDE.md が wait-and-demand で生まれた事実は、本ボルトの CLAUDE.md 運用ルールも「使う中で必要になったもの」であるべきことを示唆。plan mode が「please don't code」1文なのは驚きだが、機能の正体が極めて薄いことを示す好例。「subagent スワームが週末で plugin を完成」は [[08_Claudeを会社にする 42スキルの組織図]] を Anthropic 内部で実践した事例。「Claude code transcript を採用材料に」は、agent 時代の新しいスキル評価の萌芽。Light Cone は YC 向けなので起業家視点が強く、[[07_Boris対談]] の企業導入視点と補完関係。
