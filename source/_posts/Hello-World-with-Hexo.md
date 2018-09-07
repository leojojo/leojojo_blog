---
title: Hello World! with Hexo
date: 2018-09-07 17:57:02
categories: misc
tags: front-end
---

Static Site Generatorのチョイスがjekyll以外にもたくさんあるご時世だが、諸事情によりHexoで書いてみた。

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
ここで早速テーマを導入する。
こちら [sabrinaluo/hexo-theme-replica](https://github.com/sabrinaluo/hexo-theme-replica) を使わせていただいた。

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

この状態では記事を公開している`public`内のファイルが変更前のテーマに合わせて生成されているので、変更後のテーマに合わせて生成し直す。
```shell-session
$ hexo clean
$ hexo generate
$ hexo server
```

これで [localhost:4000](localhost:4000) に新しいテーマで立ち上がった。

## New Posts
早速、最初のブログ記事を書きたい。
```shell-session
$ hexo new post "Hello World! with Hexo"
```
""(クォート)は単語1つならあっても無くても良いが上のようなタイトルだと、""を外すと複数の`.md`ファイルが生成されてしまう。

すると`leojojo_blog/source/_posts/`下に`Hello-World-with-Hexo.md`というファイルが生成された。
開いてみると、メタ情報がyaml Front-matter形式で書かれている。使用したテーマではタグとカテゴリーを使うようなので、そこだけ書いた。
```yaml
---
title: Hello World! with Hexo
date: 2018-09-07 17:57:02
categories: misc
tags: hexo tutorial
---
```

その下にはmarkdown形式で記事を書く。[Github Flavored Markdown](https://qiita.com/qurage/items/a2f3f52c60d7c64b2e08)に対応しているのでシンタックスハイライトなども容易にできる。

### postsの削除
自分はよく色々なものを書き損じるので削除の仕方もここに書いておく。
```shell-session
$ rm leojojo_blog/source/_posts/Hello-World-with-Hexo.md
$ hexo clean
$ hexo generate
```

## New Page
テーマには Categories と Tags というタブがあるのにも関わらず、クリックするとエラーになってしまうのはお気づきだろうか。
次はこちらのページを書いていく。
```shell-session
$ hexo new page categories
$ hexo new page tags
```

とりあえずはこれだけで大丈夫。
あとは3日坊主にならずに書いていくだけだ。

### テーマの編集
このテーマには、押せそうで押せないボタンなどがたくさんある。テーマに手を加えていけば作り込んでいくことも出来るだろう。
元のテーマをforkしてみるといいかもしれないが、今回は割愛する。

## 公開
このブログを公開したい。静的ファイルのホスティングといえばgithub pagesだったが、最近はnetlifyというのが流行りらしいので試してみる。
プライベートレポジトリからのデプロイ、CIを提供してくれるなどと至れり尽くせりだ。
