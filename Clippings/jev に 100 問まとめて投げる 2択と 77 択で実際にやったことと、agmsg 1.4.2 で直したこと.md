---
title: "jev に 100 問まとめて投げる: 2択と 77 択で実際にやったことと、agmsg 1.4.2 で直したこと"
source: "https://x.com/fujibee/status/2103062720161751475"
author:
  - "[[@fujibee]]"
published: 2026-09-24
created: 2026-09-24
description: "1.4.0 で、Jev をエージェントチームのメンバーにする ext-tool を出した。そのときの記事で、安全フックのログ 889 件を 30 件ずつ jev に判定させた話を書いた。Koichi@fujibee·9月22日 記事Jev をエージェントチームの「同僚」にする: ..."
tags:
  - "clippings"
---
![画像](https://pbs.twimg.com/media/HS-KYxeagAArd40?format=jpg&name=large)

1.4.0 で、Jev をエージェントチームのメンバーにする ext-tool を出した。そのときの記事で、安全フックのログ 889 件を 30 件ずつ jev に判定させた話を書いた。

> 9月22日

あれから、この使い方でまだ試行錯誤している。いじっていると、記事を書いた時点では見えていなかった問題がいくつか出てきて、1.4.2 でそれを直した。直した中身は最後に書く。先に、実際に何をやったかを、読んだ人が同じことをできる粒度で書いておく。1通に何十問も入るようになって、jev の使い方が変わったからだ。

# やったこと: 公開データセットで、agmsg の1通に何問入るかを測る

やり方はこうだ。判定したい文を何十個も集めて、それを全部まとめた JSON を1つ作り、agmsg のメッセージ1通として jev-agent に送る。100 問でも1通。返事も1通で、その中に 100 問ぶんの答えが入って戻ってくる。1問ずつ 100 通送るのではない。この記事で「1通に何問入るか」と言っているのは、この agmsg のメッセージ1通のことだ。

889 件のログは自分の環境にしかないので、誰でも同じことができるように、公開データセットを2つ使った。片方は選択肢が2つ、もう片方は 77 個ある。データの取り方から質問の組み方まで下に全部書いてあるので、agmsg に jev-agent を入れている人は、ぜひ自分の環境でも試してみてほしい。1セントで済む。知りたかったのは4つ。1通に何問まで入るか、時間はどれくらいか、どこで断られるか、どれくらい当たるか。

**使ったデータ**

- SST-2: 映画の短評1文に「肯定 / 否定」のラベルが付いたデータ。Hugging Face の stanfordnlp/sst2、validation の先頭 100 件(否定 48 / 肯定 52)
- Banking77: 銀行アプリへの問い合わせ文に、77 種類の意図(「カードを有効化したい」「ATM で引き出せない」など)のラベルが付いたデータ。Hugging Face の mteb/banking77、test(3,076 件)から 30 件おきに 100 件

Banking77 を先頭から 100 件取ると意図が3種類しか入らない(ラベル順に並んでいる)ので、間引いて 77 種類中 75 種類が入るようにした。77 個のラベル名は legacy-datasets/banking77 の info から取った。どちらも Hugging Face の datasets-server の REST API で取れる。キーは要らない。

中身はこういうものだ。SST-2 の先頭5行(左が正解ラベル、右が jev に見せる文)。やることは「この文を読んで、肯定か否定かを当てる」。

```plaintext
positive  it 's a charming and often affecting journey .
negative  unflinchingly bleak and desperate
positive  allows us to hope that nolan is poised to embark a major career as a commercial yet inventive filmmaker .
positive  the acting , costumes , music , cinematography and sound are all astounding given the production 's austere locales .
negative  it 's slow -- very , very slow .
```

Banking77 の、30 件おきに取った先頭5行。やることは「この問い合わせを読んで、77 種類の意図のどれかを当てる」。

```plaintext
card_arrival                      How do I locate my card?
card_arrival                      My card hasn't arrived yet.
card_linking                      How do I link to my credit card with you?
exchange_rate                     what are exchange rates
card_payment_wrong_exchange_rate  I purchased something in a foreign currency but the rate applied is wrong
```

つまり、2択のほうは「感想文の肯定/否定」、77 択のほうは「問い合わせの振り分け」で、どちらも「文を1つ渡して、決まった選択肢から1つ選ばせる」という同じ形の仕事だ。安全フックのログを「妥当 / 過剰 / 保留」に分けたのと同じで、選択肢の数が違うだけになる。

**1通に入れる質問の組み方**

ここから先は、頼まれたエージェントが中でやっていることだ。jev-agent に送るメッセージ1通の本文は JSON 1個。state に全体の状況を書き、questions の中に1問ずつ名前を付けて並べる。各問は type: "choice"、instructions(何を判断するか)、criteria(選択肢名 → 説明のオブジェクト)の3つ。100 問なら questions の中に line\_1 から line\_100 まで 100 個並べる。質問同士は独立で、順番に意味は持たせていない。

SST-2 の1問はこう(実物)。

```json
{
  "state": "You are classifying movie-review sentences by sentiment. Each numbered question below is an independent, unrelated sentence -- answer each one on its own.",
  "questions": {
    "line_1": {
      "type": "choice",
      "instructions": "Classify the sentiment of this movie-review sentence: \"it 's a charming and often affecting journey . \"",
      "criteria": {
        "positive": "the sentence expresses a positive opinion or sentiment",
        "negative": "the sentence expresses a negative opinion or sentiment"
      }
    }
  }
}
```

Banking77 は criteria に 77 個の意図を全部並べる。ラベル名をキーにして、説明は下線をスペースに置き換えただけの短いものにした(先頭5つ、実物)。

```json
{
  "activate_my_card": "activate my card",
  "age_limit": "age limit",
  "apple_pay_or_google_pay": "apple pay or google pay",
  "atm_support": "atm support",
  "automatic_top_up": "automatic top up"
}
```

この 77 個を、1問ごとに毎回まるごと繰り返す。1問目にだけ書いて2問目からは省略、ということはしていない。これが 77 択で入力トークンが跳ね上がる直接の原因で、後で効いてくる。

書き方の注意は 1.4.0 の記事と同じで、instructions と criteria は英語で、選択肢の境界がはっきり分かる説明にする。

**1通で送って、1通で受け取る**

自分が打ったのは、いつものエージェントへの一言だけだ。「この 100 件、jev-agent に投げて肯定か否定か判定して」。

そのあとはエージェントの仕事で、上の JSON を組み立ててファイルに書き、それを agmsg のメッセージ1通として jev-agent に送る(agmsg の send を使う。中身は 1.4.0 の記事で書いた「jev-agent 宛てのメッセージがそのまま呼び出しになる」の仕組みそのまま)。送った側のエージェントは待たずに次の作業に戻り、返事が届いたときに1ターン起きて、それを読む。

返事は jev-agent から、これも1通のメッセージとして届く。1.4.2 からは、2問以上なら1問1行で、10 問なら 10 行、100 問なら 100 行が1通に入っている。SST-2 の 10 問を投げたときの実物がこれ(先頭3行と最後の行)。

```plaintext
jev: line_1=positive (p=1.00, confidence=1.00)
line_2=negative (p=0.99, confidence=0.97)
line_3=positive (p=1.00, confidence=0.99)
…
line_10=negative (p=1.00, confidence=1.00) (cost $0.000048)
```

料金は最後の行の末尾に1回だけ付く。形の崩れた答えがあれば、その行だけ line\_7=error (malformed answer) になって、残りは普通に返る。送りすぎたときは全体が jev-agent: processing failed (jev: request too large for one call (max\_tokens\_exceeded) -- split the questions into smaller batches) の1行で返る。

受け取ったエージェントは、この行をそのまま読んで、確信度の低い行だけ人に回す、error の行だけ再送する、という仕分けを自分でやる。ここも人は見ていない。

**自分のチームで投げた結果**

まず、自分の agmsg チームの jev-agent に、上の形で実際に投げた結果から。

- SST-2 の 10 問を1通: 送ってから返事が届くまで 2 秒。10 行返って、10 問とも正解。料金 $0.000048
- Banking77 の 45 問を1通: 3 秒。45 行返って、39 問正解(87%)。確信度 0.7 未満が 7 行。この 7 行が「人が見るべき候補」として名指しで分かるのが、1問1行になったことの実利だ
- Banking77 の 100 問を1通: 断られて、「1回に入る量を超えた、分けて送って」の1行が返る。料金はかからない

正解は、データセットに付いているラベルと jev の答えが完全に一致するかで数えた。error の行は出なかった。

上限がどこにあるかは、問数を変えて何回も投げて探った。その数字が下の表で、時間は API の往復、料金とトークンは API の返事に入っている値をそのままだ。各条件1回ずつなので、数字は目安として読んでほしい。

**上限を探った結果**

![画像](https://pbs.twimg.com/media/HS-TSKvacAAPXy8?format=jpg&name=large)

分かったことを順に。

時間は問題にならない。いちばん遅かった成功でも 2.42 秒で、100 問の2択は 0.50 秒だった。

上限は「何問まで」ではなく「1回の入力の合計」で、だいたい 6.5 万トークンのところにある。選択肢の説明を1問ごとに繰り返し送っているので、選択肢が多いほど1通に入る問数が減る。2択なら 100 問以上入るが、77 択だと 45 問で頭を打つ。

断られるときは、毎回同じ形で、すぐ返ってくる(約 0.3 秒)。料金もかからない。途中で切れたり、壊れた答えが返ったりはしなかった。だから「大きめに投げて、断られたら半分にする」という雑なやり方でも損はしない。

正解率は、2択で 99〜100%、77 択で 84〜90%。77 択で問数を増やすと少しずつ下がっている(10 問で 90%、45 問で 84%)が、1回ずつの計測なので、傾向と言えるかはまだ分からない。

計測全体でかかった料金は $0.0096。1セントだった。

測っていないこと。100 問を超える場合、77 択より多い選択肢の場合、TypeSafe の直の API での上限。

**目安として**

この結果から、jev の USAGE.md に「1回に送れる量の目安」を書いた。要点はこうだ。

- 選択肢が少ない(2〜5択)なら、100 問を1通にしてよい
- 選択肢が多い(数十択)なら、40 問前後から始めて、断られたら半分にする
- 断られても料金はかからないので、上限を探るのは安い
- 返事は1問1行なので、受け取った側は行ごとに読めばよい。error の行だけ拾って再送すれば、残りは捨てなくていい

889 件のときは 30 件ずつで 30 通だった。いまなら2択相当の判定なら 9 通で済む。

# 何が変わったか

1.4.0 の記事で、先に書いておいた弱点がある。jev は 0.2〜0.3 秒で答えるのに、その答えを受け取るには送った側のエージェントを1ターン起こさないといけない。1問のために1ターン払うのは高い、という話だった。

1通に何十問も入るようになって、この計算が変わった。1ターンのコストは同じでも、その1ターンで受け取れる答えが 100 個になる。エージェント同士のやりとりの途中で、思いついたときに「この 50 件、jev に聞いといて」と投げても、もとが取れる。ファイルから 100 行取ってそれぞれを判別する、というような、事前に仕込んでいない、その場のまとめ投げに使える道具になった。これが 1.4.2 でいちばん変わったところだと思う。

# 1.4.2 で直したこと

上の使い方をしていて当たった問題は3つで、1.4.2 でこう直した。

- 2問以上なら、1問1行で返す。これまでは答えが1行に全部つながって返ってきていて、呼んだ側のエージェントがそれをまた分解することになっていた。1問だけのときの返し方は、1文字も変えていない
- 壊れた答えがあっても、全体を失敗にしない。これまでは 100 問中1問の形が崩れると全体がエラーになって 100 問ぶんが丸ごと失われていた。その1問だけ error にして、残りの 99 問は普通に返す
- 送りすぎたときは、理由と対処を言う。これまでは「unexpected HTTP 400」とだけ返ってきて、何が悪いのか分からなかった

3つ目には、おまけの話がある。この記事を書きながら 77 択の 100 問を自分のチームの jev-agent に投げたら、最初の直しでは「分けて送って」の1行が出なかった。API 自体はちゃんと max\_tokens\_exceeded で断っていたのに、アダプタが読む場所を間違えていた。OpenRouter 経由だと 400 の本文が、エラーの種類をさらに JSON 文字列の中に包んだ形で返ってくるのに、計測のときの記録には内側の形しか残っていなくて、そこだけを見ていたからだ。テスト用の偽の返事も内側の形だけだったので、テストでは気づけず、実際の API で投げて初めて見つかった。これは 1.4.2 に入る前に直した。記録に残した形が、そのまま実装の仕様になる、という小さな教訓だった。

ほかに、レビューで見つかった安全面の直しが1つ。返事の1行に、改行だけでなく、端末を操作する制御文字やタブも混ざらないようにした。返事はそのままターミナルやログに流れるので、ここは塞いでおくべきところだった。

新しい設定は増やしていない。待ち時間を設定で変えられるようにする案もあったが、上の計測で不要と分かったのでやめた。

# 最後に

ext-tool はまだ実験中で、こういう「実際に使ってみて直した」が続く。使ってみて引っかかったところがあれば、issue に書いてほしい。今回の3つも、全部そこから出てきたものだ。

[https://github.com/fujibee/agmsg/releases/tag/v1.4.2](https://github.com/fujibee/agmsg/releases/tag/v1.4.2)

[#agmsg](https://x.com/search?q=%23agmsg&src=hashtag_click)