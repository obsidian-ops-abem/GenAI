---
title: "Post by @fujibee on X"
source: "https://x.com/fujibee/status/2083113453674459295"
author:
  - "[[@fujibee]]"
published: 2026-07-31
created: 2026-07-31
description: "agmsg v1.1.12 をリリースしました。今回の目玉は機能というより、コミュニティです。最近特に「一緒に作ってる」という度合いが一段上がってきたので、その話をさせてください。 1つめ。opencode がモニターモードに対応しました。これ、本当に自分はほとんど何もしていま"
tags:
  - "clippings"
---
agmsg v1.1.12 をリリースしました。今回の目玉は機能というより、コミュニティです。最近特に「一緒に作ってる」という度合いが一段上がってきたので、その話をさせてください。

1つめ。opencode がモニターモードに対応しました。これ、本当に自分はほとんど何もしていません。@JulikaAiblinger さん(GitHub: tsukimiya)が、opencode に監視の仕組みが無いという課題に対して、opencode-sentinel というプラグインを自分で作り、agmsg 側の対応も fork で実装して、設計記録(ADR)まで書いて「本家に還元すべきか」を自分で検討していました。X で見かけて「PR ください」とお願いしたら、翌日に2本届きました。sentinel が無い環境では従来の動きに自動で切り替わる配慮まで、最初から入っていました。

2つめ。「codex ブリッジが動かない。1日潰して諦めた」という報告が X で流れてきました。東リ屋さん(@8CEVSmSRMT32119)です。諦めたと言いつつ、そのあと note に真因をコードレベルで特定した記事を書いてくれました。Windows の環境によっては起動だけで29秒かかり、タイムアウトの起点が起動より後に計算されるせいで必ず強制終了する、という機序を実測で追い切ってあった。おかげでこちらの修正は2行、報告から修正の着地まで、1日で行けました。

3つめ。agmsg のメッセージを「見る」ツールが、コミュニティ製だけで5つになりました。以前報告されていた agmsg-bubblelog(@dreiachse さん。Claude Code と Codex のやり取りを LINE 風に眺められる。追加遅くなってすみません！)と agmsg-tui(rrrrnmtsu さん。Rust の ratatui 製で、SSH や tmux の中から使える)。どちらも http://agmsg.cc のショーケースに載せました。同じメッセージの土台の上で、見せ方が独立に育っていくのは、眺めていて楽しいです。

このほか今回のリリースには、@Masashi\_Ono0611 さん(project\_path の検証)、@chemica\_tan さん(Windows の誤判定2件の修正)、@otsune さん(PowerShell から安全に使う書き方の文書化)の貢献も入っています。今回も外部の貢献が8人分以上入りました。

作っていて感じるのは、コミュニティが agmsg に強い輪郭を与えてくれていることです。opencode 対応は完全にコミュニティ発でしたし、Windows の重い環境の問題は、実際に使っている人の実測がなければ直せませんでした。ありがとうございます！

こういうコードレベルで関わってくれる人たちには、何かしらのお返しができるときに、ちゃんとしたいと思っています。

npx agmsg@latest install --update で入りますので、また何かあればお知らせください！

http://github.com/fujibee/agmsg

#agmsg

[agmsg.cc agmsg — Cross-agent messaging for CLI AI agents](https://t.co/kdJWfbAdiH)