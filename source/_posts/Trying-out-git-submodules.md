---
title: Trying out git submodules
date: 2018-09-08 01:10:00
categories: blog
tags: hexo, git
---

前回の記事 [Hello World! with Hexo](https://blog.leojojo.me/2018/09/07/hello-world-with-hexo/) で使ったテーマはgithubからクローンしてきたものなので、変更をgit管理しようとするとレポジトリの中にレポジトリがある状態になる。
これをするとcommitしたときに、gitが注意してくる。
```
$ git commit -m "change theme"
warning: adding embedded git repository: child
hint: You've added another git repository inside your current repository.
hint: Clones of the outer repository will not contain the contents of
hint: the embedded repository and will not know how to obtain it.
hint: If you meant to add a submodule, use:
hint:
hint:   git submodule add <url> child
hint:
hint: If you added this path by mistake, you can remove it from the
hint: index with:
hint:
hint:   git rm --cached child
hint:
hint: See "git help submodule" for more information.
```

どうやらサブモジュールというのでレポジトリをネスト出来るらしい。

## とりあえずやってみた
詳しい説明はこのページに任せます: [Git - サブモジュール](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%81%95%E3%81%BE%E3%81%96%E3%81%BE%E3%81%AA%E3%83%84%E3%83%BC%E3%83%AB-%E3%82%B5%E3%83%96%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB)
一言でサブモジュールの特徴をあげるとすると、サブモジュールはコミット単位で参照されること。

```shell-session
$ git submodule add git@github.com:sabrinaluo/hexo-theme-replica.git themes/replica
```

とりあえずこれで良さそうだ。

## 自分のレポジトリをサブモジュール管理したくなった
先程追加したのはsabrinaluo様のレポジトリだ。
自分がテーマに加えた変更もgit管理したいので、githubでforkした。
![fork on github](/images/Trying-out-git-submodules/1.png)

### 失敗
```shell-session
$ git submodule deinit themes/replica
```
とすればいいようなことが書いてあったが、エラーを吐かれて、普通に`rm -rf`してくれと書いてあってのでそのとおりに従った。
しかし、問題がなさそうなのでこのままデプロイしてみようとすると、`git clone`に失敗してしまった。
以前のコミットへの参照は消えていないよう。

### 成功
```shell-session
$ rm .gitmodules
$ git rm -r themes/replica
$ git submodule add git@github.com:leojojo/hexo-theme-replica.git themes/replica
```
これで目的のレポジトリがcloneし直され、正しくサブモジュールになったようだ。
一度消した`.gitmodules`も生成され直されている。
やったー

![テーマも微編集できた](/images/Trying-out-git-submodules/2.png)
これでブログの細かい部分も直せた。
