---
title: "Post by @hkunimitsu on X"
source: "https://x.com/hkunimitsu/status/2102906502566772748"
author:
  - "[[@hkunimitsu]]"
published: 2026-09-24
created: 2026-09-24
description: "最近、AI界隈で話題になっている「Jev」、詳しく調べました。 最初に結論から言うと、個人が今すぐJevを直接使う理由は、ほとんどないです。普通にChatGPTやClaudeを使ってResearchしたり、文章を書いたり、仕事を手伝ってもらうなら、それで十分。 JevはCha"
tags:
  - "clippings"
---
最近、AI界隈で話題になっている「Jev」、詳しく調べました。

最初に結論から言うと、個人が今すぐJevを直接使う理由は、ほとんどないです。普通にChatGPTやClaudeを使ってResearchしたり、文章を書いたり、仕事を手伝ってもらうなら、それで十分。

JevはChatGPTの代わりになるAIではありません。

では、なぜ話題なのか？

Jevが面白いのは、「AIが裏側で何百万回、何千万回と判断するときに使うAI」だからです。

今のLLMは文章を書く、深く考えるだけでなく、「次はどのTool？」「retryする？」「安全？」「高性能モデルが必要？」といった小さな判断にも使われています。

AI Agentが普及すると、1回の依頼の裏側でこうした判断が10回、50回、100回と発生する。

1000万ユーザーが1日10回Agentを使い、裏で20回判断すれば、1日20億decision。

ここで毎回LLMを動かすのはかなり無駄です。

Jevは、この「判断」だけを切り出したモデル。

LLMのように文章を1tokenずつ生成せず、「A、B、Cのどれ？」「YESの確率は？」「どの行動？」を直接返す。

例えば、営業3%、請求92%、サポート5%。

ざっくり言えば、

LLM＝「次に何を書くか」を予測するAI

Jev＝「どの選択肢が正しい可能性が高いか」を予測するAI

従来のClassifierはSpam、Fraudなど用途ごとに専用モデルが必要でしたが、Jevは文章＋質問＋選択肢を渡せば汎用的に判断できる。

いわば「汎用Decision Model」です。

さらに重要なのがCalibration。

LLMに「何%自信がある？」と聞いて90%と答えても、その数字は基本的には生成された文章です。

Jevは「90%と判断したケースなら、実際にも約90%正しい」という状態を目指す。

これなら、99%以上→自動実行、90〜99%→別AIで確認、それ以下→人間へ、のようなAgent運用がしやすい。

学習方法も面白い。

TypeSafeによると、JevはほぼSynthetic Dataで学習しています。「この表現に弱い」「この条件で間違える」という部分を見つけ、その周辺の問題を人工的に大量生成して鍛える。

つまり、「必要な判断能力を鍛える訓練問題を量産する工場を作る」という発想です。

TypeSafeのCEO、Diogo Almeidaは元OpenAI、元Google Brainで、InstructGPTやRLHFの研究に参加していた人物。

ChatGPTにつながる「人間にとって良い回答を返すAI」を作った側の人が、今度は「ソフトウェア内部の小さな判断まで文章生成AIでやる必要があるのか？」という逆方向の問題を解きにいっているのが面白い。

Jevが効きそうなのは、Tool選択、Model Routing、安全判定、retry/stop、AI出力評価、Moderation、Risk判定など、

「1回の判断は軽い。でも何百万回、何億回も発生する」

領域です。

例えばCoding Agent。ユーザーは「このbug直して」と頼むだけですが、裏ではRepoを見る？どのTool？Commandは安全？Testする？retryする？もう終わる？と大量の判断が走る。

ここを毎回Frontier LLMで処理する必要はない。Jev的なDecision Modelで高速処理し、難しいところだけLLMに戻せばいい。

逆に、経営判断、企画、文章作成、深いResearchなどは普通にLLMの方が向いています。

つまりJevは新しいChatGPTではなく、「ChatGPTやClaudeの裏側に入って、AI全体を速く、安く、安定させる部品」に近い。

そしてJev的な機能は、今後OpenAI、Google、Anthropicなどにも実装されていく可能性が高い。

一般ユーザーが「今日はJevを使おう」と意識することは、たぶんありません。ChatGPTやClaudeを普通に使っているだけで、裏側でTool選択、Routing、安全判定などが最適化され、「速くなった」「安定した」と感じるだけ。

そうなるとTypeSafeが独立して大きくなれるかは別問題です。「高速なClassifier」だけなら大手は自前で作れる。

一方でTypeSafeが、Jev→Model Router→Agent Router→Eval→Guardrail→Cost Optimizerまで広げ、「どのAIに、いつ、どれだけ計算させるかを決めるAI」、つまりAI時代のDecision Infrastructureを取れればかなり大きい。

僕は最終的には、個人から見ると「1つのAI」、企業の社員から見ても「会社のAIアシスタント」。でも裏側では多数のAIやModelが自動的に使い分けられる世界になると思っています。

CPU、GPU、Neural Engineを意識せずiPhoneを使うのと同じです。

Jevで一番面白いのは「新しいAIモデルが出た」ことではない。

AIが1つの巨大モデルですべてを処理する時代から、「考えるAI」「書くAI」「判断するAI」「探すAI」「実行するAI」を最適に組み合わせる時代に入り始めていること。

AI競争も「一番賢いモデルはどれか」だけではなく、「大量のAIをどう組み合わせて、最も速く、安く、正確に仕事を終わらせるか」の競争になっていく。

Jevは、その変化をかなり象徴していると思います。

PS：

また新しいAIを勉強しなきゃ、と思った方は安心してください！w

![画像](https://pbs.twimg.com/media/HS8GUSma4AA99qJ?format=jpg&name=large)

---

## Comments

> **川邊健太郎 @dennotai** · [2026-09-24](https://x.com/dennotai/status/2103052917188272451)
> 
> お忙しい國光ファンの為に！
> 
> ![画像](https://pbs.twimg.com/media/HS-Leu9bUAAdfTX?format=jpg&name=large)