---
title: Filling Stockings with Arbitary Code
tags:
---

`LD_PRELOAD`を完全に理解したくなったので、ちょっと見てみると、出来ることはどうやら`ptrace(2)`と似ているらしい。
で、結局どちらも分からないので、とりあえず`LD_PRELOAD`を触ってみた。
ちなみに`ptrace()`はだいぶプロセスやメモリやアセンブリの勉強になりそうだった(まだ理解できていない)。

## プロセスの挙動を変える
### ptrace
`ptrace(2)`は**実行中の**プロセスの挙動を変更することが出来るシステムコールだ。
これを使うことによって、あるプロセスが実行中のシステムコールを表示したり、メモリを書き換えたりしてデバッグに役立てる事ができる。
身近かもしれない例だと、デバッガのgdbの中で使われている。

### LD_PRELOAD
`ld.so`ライブラリが、プログラムの動的リンクを行う。
`LD_PRELOAD`は、全てのライブラリに先立ってリンクして欲しい共有ライブラリを指定できる環境変数だ。
これを使うことによって、ライブラリをコンパイルし直すことも無く、**実行時に**挙動を変更することが出来る。
簡単な例だと、`stdlib.h`にはランダムな整数を返す関数`rand()`があるが、`LD_PRELOAD`で読み込んだ独自の`rand()`関数内で`return 2018;`とすると固定で2018という整数を返す関数に変えてしまうことが出来る。

## 根暗ソフトウェア
進捗を暗号化してくるソフトウェアは、感染端末をめちゃくちゃにする陽キャタイプだとすると、これから紹介する根っこ大好きソフトウェアは、バレないようにひっそりする陰キャタイプだと思う。
根っこ大好きソフトウェアはカーネルモジュールだったりするらしいが、`LD_PRELOAD`タイプはユーザーランドで活動する。
古くはあるが、ポケモンのルージュラの英語名のソフトウェアが有名らしい。
![ルージュラ](/images/Filling-Stockings-with-Arbitary-Code/1.jpg)

### socket()をあれこれしてみる

---

- [\[Debug\] LD_PRELOAD, dlsym, GCC拡張機能によって共有ライブラリの関数の呼出し前後で任意の処理を実行する - th0x4c 備忘録](http://th0x4c.github.io/blog/2013/06/25/debug-override-a-shared-library-function-by-ld-preload-dlsym-and-gcc-attributes/)
- [Dynamic linker tricks: Using LD_PRELOAD to cheat, inject features and investigate programs | Rafał Cieślak&#039;s blog](https://rafalcieslak.wordpress.com/2013/04/02/dynamic-linker-tricks-using-ld_preload-to-cheat-inject-features-and-investigate-programs/)
