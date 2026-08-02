---
title: "35_agmsg CLIエージェント間メッセージング（出典）"
tags: [raw-source]
source: https://x.com/fujibee/status/2083113453674459295
repo: https://github.com/fujibee/agmsg
author: fujibee (@fujibee)
published: 2026-07-31
created: 2026-08-01
---

# 出典メタデータ

- ポストURL: https://x.com/fujibee/status/2083113453674459295
- GitHub: https://github.com/fujibee/agmsg
- ショーケース: http://agmsg.cc
- 著者: fujibee（@fujibee）
- 公開: 2026年7月31日
- 形態: X ポスト（v1.1.12 リリース報告）
- タイトル: **agmsg — Cross-agent messaging for CLI AI agents**

要約は [[02_agmsg CLIエージェント間メッセージング]] を参照。

> [!info] 本文取得について
> 本記事はユーザーが Clippings フォルダへクリップした全文を提供したため、それに基づき転記。

---

# 本文（再構成）

## v1.1.12 リリース —— 今回の目玉は「機能」でなく「コミュニティ」

> 最近特に「一緒に作ってる」という度合いが一段上がってきた。

### 1. opencode がモニターモードに対応（完全にコミュニティ発）

@JulikaAiblinger さん（GitHub: tsukimiya）が、opencode に監視の仕組みがないという課題に対し:
- **opencode-sentinel** というプラグインを自作
- agmsg 側の対応も fork で実装
- 設計記録（ADR）まで書いて「本家に還元すべきか」を自ら検討
- Xで見かけた @fujibee が「PRください」と依頼 → 翌日に2本届いた
- **sentinel がない環境では従来の動きに自動で切り替わる配慮**が最初から入っていた

### 2. codex ブリッジの修正（コミュニティの実測が真因特定）

- 東リ屋さん（@8CEVSmSRMT32119）から「codex ブリッジが動かない。1日潰して諦めた」と報告
- その後 note に**コードレベルで真因を特定した記事**を投稿:
  - Windows の環境によっては起動だけで **29秒**かかる
  - タイムアウトの起点が起動より後に計算されるため**必ず強制終了**する機序を実測で追切った
- おかげで @fujibee 側の修正は **2行**。報告から修正着地まで1日。

### 3. agmsg のメッセージを「見る」ツールがコミュニティ製だけで5つに

- **agmsg-bubblelog**（@dreiachse さん）—— Claude Code と Codex のやり取りを **LINE 風**に眺められる
- **agmsg-tui**（rrrrnmtsu さん）—— Rust の ratatui 製、SSH/tmux の中から使える
- どちらも http://agmsg.cc のショーケースに掲載
- 同じメッセージ土台の上で、**見せ方が独立に育っていく**

### 今回の外部貢献（8人分以上）

- @Masashi_Ono0611 さん（project_path の検証）
- @chemica_tan さん（Windows の誤判定2件の修正）
- @otsune さん（PowerShell から安全に使う書き方の文書化）

## 所感（著者）

> コミュニティが agmsg に強い輪郭を与えてくれている。opencode 対応は完全にコミュニティ発だったし、Windows の重い環境の問題は、実際に使っている人の実測がなければ直せなかった。

## インストール

```
npx agmsg@latest install --update
```

---

## 位置付け

CLI で動く AI エージェント（Claude Code / Codex / opencode 等）間でメッセージをやり取りするための OSS。エージェント間連携のインフラであり、コミュニティによる周辺ツール（可視化・監視）が独立に育つエコシステムを形成している点が特徴。v1.1.12 は機能追加でなく**コミュニティ主導の成熟**を示すリリース。

---

## 関連（ボルト内）

- マルチエージェント協調 → [[02_1チャットをエージェントチームへ Opus5 12ステップ]]
- エージェント運用基盤 → [[02_24時間自走する自律型AIエージェントの設計図]]・[[01_エージェントファクトリの作り方 ビルダーズガイド]]
