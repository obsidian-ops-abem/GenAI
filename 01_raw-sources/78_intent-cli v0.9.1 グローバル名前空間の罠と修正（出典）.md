---
title: "78_intent-cli v0.9.1 グローバル名前空間の罠と修正（出典）"
tags: [raw-source]
source: https://x.com/tomohisa/status/2084149093027455289
author: tomohisa（@tomohisa）
published: 2026-08-03
created: 2026-08-04
---

# 出典メタデータ

- URL: https://x.com/tomohisa/status/2084149093027455289
- 著者: 高丘 @tomohisa（ジェイテックジャパン CTO）
- 公開: 2026年8月3日（Xポスト・リリースノート）
- 関連: [[71_intent-cliでHerdr開発 止まらないオーケストレーション（出典）]] の続報。v0.9.0→v0.9.1

---

# 本文

intent-cli v0.9.1をリリースしました。先日 0.9.0 を出したあと、別チームが実際に2チーム目を立てようとして詰まりました。その修正です。

herdr のエージェント名はマシン全体でグローバル一意です。一方 intent-cli の通知は「ロール名と同名のエージェント」を探していました。つまり implementation や review という正規の名前は、マシン上の1チームしか持てない。2チーム目の通知が必ず壊れる構造でした。

気づいた決め手が面白くて、このマシンの4つのワークスペースが全部違う命名を発明していたんです。implementation / implement / sekiban-implementation。誰も同じ名前を使えていないのは、グローバル名前空間の署名でした。

修正は、受信者の同一性を「記録された workspace + pane」にしたこと。ロール名がエージェント名から独立し、複数チームがそれぞれ正規の名前を使えます。

もう一つ、記録が無いことを「検査不要」ではなく「検査未了」として扱うようにしました。設定が未記録のチームは、これまで健全性チェックを素通りしていました。

agmsg 版も引き続きご利用いただけます。

[github.com Release intent-cli v0.9.1 · J-Tech-Japan/intent-system](https://t.co/F5Oya2VciG)
