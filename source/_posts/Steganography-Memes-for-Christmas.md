---
title: Steganography Memes for Christmas
date: 2018-12-29 23:55:35
categories: security
tags:
- C&C
- image
- twitter
- python
---

<a href="https://blog.trendmicro.co.jp/archives/20020" class="embedly-card" data-card-image="0" data-card-controls="0" data-card-align="left"></a>
これを読んで、「うわ、ツイッターじゃん、たのしい」と条件反射的に思ってしまったのでやってみた。

## ステガノグラフィ
### データを隠す
![Mr.Robot](/images/Steganography-Memes-for-Christmas/1.png)

ステガノグラフィというのはデータを、別の関係の無いデータの中に隠してしまう手法だ。
[Mr.Robot](http://amzn.asia/d/3LAuLzk)では、主人公エリオットくんがオーディオのファイルフォーマットであるFLACに、画像ファイルやらテキストファイルやらを詰め込んでいるシーンが何度も出てくる。
良くCDを焼いては、表面にバンド名などをマーカーで書いているあのシーンは、ステガノグラフィを使ってデータを隠しているのだ。
隠しているファイルの事をペイロード、隠れ蓑となっている(この例だとオーディオファイル)ファイルの事をキャリアと呼ぶ。
(Mr.Robotは実在するツールや手法をふんだんに使っているリアルさと、ミステリアスなストーリー展開、そして主人公の演技が素晴らしいのでぜひ見てみてほしい。)

<a href="https://www.amazon.co.jp/dp/B015NZFF8I/ref=cm_sw_r_cp_ep_dp_pIuiCb3H6T9CY" class="embedly-card" data-card-image="0" data-card-controls="0" data-card-align="left"></a>

そんな面倒な手法なんて使わずに、暗号化すれば隠したいデータにはアクセス出来なくなるのに、と思われるかもしれない。
しかし、暗号化されたデータは非常に目に付きやすいのに対して、ステガノグラフィはそもそも隠されたデータがあることにすら気が付かせない点が優秀だ。
SNSを通信手法として使う時に、通信内容がバレること以上に避けたいのはアカウントを凍結されることなため、うってつけといえる。

### 画像
#### LSB
画像にはピクセルで表現するビットマップ画像と、アンカー間の直線曲線で表現するベクトル画像があって、どちらでもステガノグラフィは可能ではあるが、今回はより簡単で一般的なビットマップ画像を扱う。
ピクセル毎に色情報があるが、その色を表すビット列の桁が高いほど重要な情報で、低いほど多少弄っても色に大きな変化は無い。
キャリアのRGB情報の最下位ビットがあった場所に、ペイロードを埋め込む手法をLSB(Least Significant Bit)ステガノグラフィと呼ぶ。

![ステガノグラフィ](/images/Steganography-Memes-for-Christmas/2.png)

#### ファイルフォーマット
次にファイルフォーマットについて。
シンプルだがファイルサイズがとても大きいRAW画像、PNGをはじめとする可逆圧縮画像、そしてJPGをはじめとする不可逆圧縮画像がある。
JPGはブロックを周波数に変換、量子化による情報量削減、そして符号化による圧縮というステップを踏んで生成される。
ツイッターは画像をアップロードしても、後述する対策を施していないと低画質JPGに変換するので、情報量削減のステップでステガノグラフィが壊れてしまう。

## やってみた
<a href="https://github.com/leojojo/twitter_cc_steg" class="embedly-card" data-card-image="0" data-card-controls="0" data-card-align="left"></a>

### 準備
クリスマスっぽい画像`carrier.png`と、埋め込みたいテキスト`payload.txt`を用意する。
(一瞬でも疑問に思った方はお正月っぽい画像を用意してください。僕はクリスマスプレゼントが届くまでは諦めません…)
サーバーとクライアントを用意する、ターミナル画面が2つあるだけでも大丈夫。
こんな`payload.txt`を用意した。自分の端末のスクリーンショットを、クライアント側からサーバー側の8080番ポートへと送る設定をこう記述してみた。
```
<IPアドレス>:<ポート>/<アクション>
```
アクションはプロセス一覧を送る`processes`とスクリーンショット画像を送る`screenshot`を用意した。

### サーバー側
```bash
git clone https://github.com/leojojo/twitter_cc_steg.git
cd twitter_cc_steg
pip -r requirements.txt
python steg.py -c server/carrier.png -p server/payload.txt
chmod +x ./server/server.sh
./server/server.sh
```
出力された`output.png`をツイッターに手動で上げる。

### クライアント側
```bash
git clone https://github.com/leojojo/twitter_cc_steg.git
cd twitter_cc_steg
pip -r requirements.txt
cp client/config_sample.py client/config.py
python client/check_twitter.py
```
`config.py`には自分のtwitter IDを入力する。
`check_twitter.py`スクリプトは`crontab cron.conf`で5分に1回実行することもできる。

### 結果
クライアント側に画像が落とされ、ペイロードが抽出できればスクリーンショットが撮影される。
サーバー側では8080番ポートにbase64で画像が送られてきて、保存される。
(画像を送ってもテキストを送っても無理矢理両方保存されてしまうのはご愛嬌。)

![ゴトランド](/images/Steganography-Memes-for-Christmas/3.png)

ブラウジング中の様子ががっつり写ってしまいましたね。

## 解説
### C&Cのクライアント側
C&C(Command & Control)サーバーというのは、マルウェアの司令塔となるサーバーの事だ。
C&Cが謎のドメイン、謎のIPのサーバーだとアラートが鳴ったり、見覚えがないので管理者が怪しんだり、そもそもアクセスが出来なかったりするが、SNSをC&Cとして利用すると一般的な利用と区別が付きにくい。

C&C出来るかやってみたいだけなので、マルウェアではなく、安全なpythonのスクリプトを書く。
1. ツイッターアカウントを定期的に見に行く
2. 画像が上がっていたらダウンロードしてくる
3. 画像からデータを取り出す
4. データで指定されているツイッターアカウントにスクリーンショットをアップロードする

データを取り出す部分で冒頭で述べたライブラリを使わせて頂く。
また、意外と重要な知見として、Twitter APIは改変に改変を重ねた結果、最近ツイートしていないアカウントや作りたてのアカウントのツイートを返さないなど、この用途にはかなり扱いにくくなっている。
そのため、seleniumを使ったブラウザ自動操作で、twitter.comを見に行った方が良い。

### ステガノグラフィ画像の生成
画像フォーマットの時にした説明をより詳しくすると、ツイッターにアップロードされたjpgやアルファ値が一定のpng画像は全て画質の低いjpg画像に変換・圧縮される。
SNSが得意な人達にとっては1pxだけ透明なピクセルを入れたり、画像全体のアルファ値を255(完全に不透明)ではなく254にするのは常識らしく、驚いた。
ステガノグラフィのライブラリはたくさんあったが、いくつか試したものがRGBAではなくRGBのみで画像を生成していたため、結局自分でそちらも書くことにした。

#### ピクセルいじり
Pillowという画像操作ライブラリを使うと、こんなに簡単にピクセルがいじれる。
```python
img = Image.open('carrier.png')
pixels = list(img.getdata())
# => [(255,255,255,255),(123,45,67,255)...]
```

画像をピクセルの配列にし、ピクセル毎はR,G,B,Aを表すタプルになっている。
最下位ビットを取っていきたいので、255といった10進法の値を11111111といったバイナリに変換。
```python
for pixel in pixels:
    binary = tuple(bin(p)[2:] for p in pixel)
    # => 11111111
```

埋め込みたい文字列もバイナリに変換し、先程のバイナリの最下位ビットに上書きしてしまう。
```python
old_binary = 11111111
digit = 0
new_binary = str(old_binary)[:-1] + str(digit)
# => '11111110'
```

あとはこれを画像のフォーマットに直してやるだけで、無事に文字列を画像のLSBに埋め込めた。
取り出す際も、LSBを取ってくる原理は同じだ。
文字列の先頭に文字列の長さを入れておいたので、その長さ分だけLSBを取り出して、バイナリから文字列に戻す。
`10\0ABCDEFGHIJhogehogefoo`だったら、10文字分の`ABCDEFGHIJ`といった具合にしてみた。

## おわりに
C&Cは見つからないようにするアイディアが豊富で面白い。
今回は投稿をコマンドのトリガーとしているわけだが、投稿の「削除」をトリガーにすることで証拠も残らなくする方法もあるようだ(ソースは忘れてしまいました)。
全くもってマルウェアではないクライアントを書いてそこからツイッターにアクセスしたが、準備も結構必要になってしまったし、見つかりやすそうだ。
マルウェアだったら、クライアント側も遥かに隠密な方法でアクセスするのだろうなと思った。
