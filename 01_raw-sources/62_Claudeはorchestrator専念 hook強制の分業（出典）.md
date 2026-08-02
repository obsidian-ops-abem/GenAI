---
title: "62_Claudeはorchestrator専念 hook強制の分業（出典）"
tags: [raw-source]
source: https://x.com/cursorvers/status/2083442835500781699
author: 大田原正幸｜医療AIガバナンス｜Cursorvers（@cursorvers）
published: 2026-08-01
created: 2026-08-02
---

# 出典メタデータ

- URL: https://x.com/cursorvers/status/2083442835500781699
- 著者: 大田原正幸｜医療AIガバナンス｜Cursorvers（@cursorvers）
- 公開: 2026年8月1日
- 形態: X ポスト（図解付き）＋リプライ
- タイトル: **私のClaudeの使い方** — Claude にコードは書かせず orchestrator 専念・worker 別モデル・hook で強制

要約は [[04_Claudeはorchestrator専念 hook強制の分業]] を参照。

---

# 原文（@cursorvers の X ポスト全文＋コメント）

「私のClaudeの使い方」

結論から言うと、私はClaudeにコードは書かせません

この図は個人開発用のものですが、Claudeの役割はorchestrator専念で、要件の対話、タスクの振り分け、成果物の検証、合議の判定だけを担います

実装やレビューの実務は他社モデルがworkerとして引き受けます

workerは、CodexやGLM、Kimi、Grokといったメジャーどころだけでなく、NVIDIA NIM (Nemotron 3 Super 120B)、さくらのAI Engine、ローカルLLMとしてQwen3.6-35B-A3Bなどを併用して、適時入れ替えしながら多様性の確保に努めています

面白いのは、この分業が心がけではなく「hook」で強制されている点です

Claudeがコードを直接編集しようとするとhookがblockし、委譲へ回るしかない

Claude内部のsubagent(Sonnet/Haiku)への丸投げもdefault-denyで封じてあり、抜け道がありません

「なぜこうするか」

それは判断と実装を同じモデルに握らせると、自分の成果物を自分で採点する構図になり、検証が甘くなるからです

高価なClaudeは判断と統合にだけ使い、手数の要る仕事は各モデルの得意分野へ流す

分業の境界を規約でなく構造で守るのが、この使い方の肝です

---

## Comments

> **みさき@AI起業家 @misaki_hennyu** · 2026-08-01
>
> Claudeに実装は書かせずorchestratorだけ、というのは自分の運用にも近いです。要件をどう対話形式で詰めているのか、その最初の質問設計だけでも参考にしたいところです。
>
> > **大田原正幸｜医療AIガバナンス｜Cursorvers @cursorvers** · 2026-08-01
> >
> > ありがとうございます。
> >
> > 正直に言うと、固定5項目の型はあるものの、毎回フルで回してはいません。
> >
> > 実際に効いているのは2つで、
> >
> > \- 質問を自由記述にせずAI側が選択肢を作って選ばせること
> >
> > \- 着手前に完了条件だけは必ず決めること
> >
> > 残りの項目は案件の重さ次第で省いています。
