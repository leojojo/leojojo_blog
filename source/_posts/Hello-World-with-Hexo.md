---
title: Hello World! with Hexo
date: 2018-09-07 17:57:02
categories: blog
tags: hexo
---

Static Site Generatorのチョイスがjekyll以外にもたくさんあるご時世ですが、諸事情によりHexoで書いてみました。

## Starting Hexo
```shell-session
$ mkdir leojojo_blog
$ hexo init leojojo_blog
$ cd leojojo_blog
$ npm install
$ hexo server
```

これだけで [localhost:4000](localhost:4000) にデフォルトの状態で立ち上がる。

## Using Themes
ここで早速テーマを導入します。
こちら [sabrinaluo/hexo-theme-replica](https://github.com/sabrinaluo/hexo-theme-replica) のかわいいテーマを使わせていただいた。

```shell-session
$ cd themes
$ git clone git@github.com:sabrinaluo/hexo-theme-replica
$ mv hexo-theme-replica replica
```

次に`_config.yml`でテーマを設定する。
```yaml
theme: landscape
```
とあるのを
```yaml
theme: replica
```
に変更した。

この状態では記事を公開している`public`内のファイルが変更前のテーマに合わせて生成されているので、変更後のテーマに合わせて生成し直します。
```shell-session
$ hexo clean
$ hexo generate
$ hexo server
```

これで [localhost:4000](localhost:4000) に新しいテーマで立ち上がった！

## New Posts
早速、最初のブログ記事を書きたい。
```shell-session
$ hexo new post "Hello World! with Hexo"
```
""(クォート)は単語1つならあっても無くても良いが上のようなタイトルだと、""を外すと複数の`.md`ファイルが生成されてしまう。

すると`leojojo_blog/source/_posts/`下に`Hello-World-with-Hexo.md`というファイルが生成されます。
開いてみると、メタ情報がyaml Front-matter形式で書かれている。今回使用するテーマではタグとカテゴリーを使うようなので、そこだけ書きました。
```yaml
---
title: Hello World! with Hexo
date: 2018-09-07 17:57:02
categories: misc
tags: hexo tutorial
---
```

その下にはmarkdown形式で記事を書いていきます。[Github Flavored Markdown](https://qiita.com/qurage/items/a2f3f52c60d7c64b2e08)に対応しているのでシンタックスハイライトなども簡単だ。
画像は`source/images`というディレクトリを作成して、その中に入れましたがもっと良い方法があれば教えてください。

### postsの削除
自分はよく色々なものを書き損じるので削除の仕方もここに書いておく。
```shell-session
$ rm leojojo_blog/source/_posts/Hello-World-with-Hexo.md
$ hexo clean
$ hexo generate
```

## New Page
テーマには Categories と Tags というタブがあるのにも関わらず、クリックするとエラーになってしまう。
次はこちらのページを書いていく。
```shell-session
$ hexo new page categories
$ hexo new page tags
```

とりあえずはこれだけで大丈夫。
あとは3日坊主にならずに書いていくだけ。

### テーマの編集
このテーマには、押せそうで押せないボタンなどがたくさんある。
元のテーマをforkして、手を加えてみるといいかもしれない。

## 公開
このブログを公開したい。
静的ファイルのホスティングといえばgithub pagesだったが、最近は[netlify](https://app.netlify.com/)というのが流行りらしいので試してみる。
プライベートレポジトリからのデプロイ、CIを提供してくれるなどと至れり尽くせり。
今回はgithubと連携してほしいのでgithubからログインしました。
![github連携](/images/Hello-World-with-Hexo/1.png)
![レポジトリ選択](/images/Hello-World-with-Hexo/2.png)
![ブランチ、ビルドコマンドは勝手に選択してくれた](/images/Hello-World-with-Hexo/3.png)

UIがこれ以上ないくらいにわかりやすい。
ビルドコマンドや公開レポジトリまで自動で選択してくれた。
なんとこの時点でもう、netlifyの ~~怪しい~~ 自動生成ドメインで公開されている。
![netlifyドメインで公開されいる](/images/Hello-World-with-Hexo/4.png)

### 独自ドメイン
自分の持っているドメインでサイトを公開したい。
[github student](https://education.github.com/pack) に登録して得られる様々な無料コンテンツのうち1つがnamecheapの`.me`ドメイン。
学生の方ははぜひ登録してみてください。

namecheapに持っているドメインをnetlifyで公開しているサイトに向けたい。
まずnetlifyで自分のドメインを入力をする。
![自分のドメインを入力した](/images/Hello-World-with-Hexo/5.png)

次に、DNSレジストラであるnamecheapでも設定をする必要がある。
[Domain List](https://ap.www.namecheap.com/domains/list/) からmanage > Advanced DNSを選んで、レコードを編集する。
netlifyにどのネームサーバーを追加すればいいかの説明があるので
>>
Nameservers
Point your domain’s nameservers at Netlify
To use Netlify DNS, go to your domain registrar and change your domain’s nameservers to the following custom hostnames assigned to your DNS zone.
>>>
dns1.p08.nsone.net
dns2.p08.nsone.net
dns3.p08.nsone.net
dns4.p08.nsone.net

画面には写りきっていないが上の通りに追加した。
![DNSレコード編集](/images/Hello-World-with-Hexo/6.png)

このまましばらく待っていたらこのドメインで、Let's EncryptによるSSL certificate付きでサイトが公開された。
やったー

![完成](/images/Hello-World-with-Hexo/7.png)

3日坊主がこわいところだがこれからはたまにかきものを公開していこうと思う。
