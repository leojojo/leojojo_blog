---
title: Some questions in Javascript
date: 2018-10-21 17:53:33
categories: 
tags: javascript
---

情報基礎2の受講者向けの簡単な問題です。
プログラムを機能ごとに、関数にしてみるのがテーマです。

HTMLはどの問題でもこちらを使い回して、ボタンを増やしていく以外はほぼ手を加えません。
```html
<textarea id="txt"></textarea>
<input type="button" id="convert" value="変換" onclick="submit()">
<li id="result"></li>
```

## 1. ツイート分割
長文をツイートしたくとも、ツイッターには1ツイートの文字数の上限があるので、何ツイートかに分けねばなりません。
テキストを140文字ずつに分割して表示するプログラムを書いてください。
これには [substr()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/String/substr) という、javascriptに元から用意してある関数が便利です。
```javascript
function textToTweet() {
  var txt = document.getElementById('txt').value;
  var num = Math.ceil(txt.length/140);
  for (i=0; i<num; i++) {
    var tweet = txt.substr(i*140, 140);
    alert(tweet);
  }
}
```

何度もalertでOKと出すのは非常に億劫なので、ページに箇条書きで表示しましょう。
```javascript
function textToTweet() {
  var txt = document.getElementById('txt').value;
  var num = Math.ceil(txt.length/140);
  var result = document.getElementById('result'); // 追加
  for (i=0; i<num; i++) {
    var tweet = txt.substr(i*140, 140);

    var ul = document.createElement('ul');        // 追加
    ul.textContent = tweet;                       // 追加
    result.appendChild(ul);                       // 追加
  }
}
```

複数の結果を箇条書きにするこの機能、便利ですね。
今後も何度も使いまわしていきたいので、別の関数に表示をする機能だけ切り出します。
少し改変して、配列、つまり複数の値が連なって定義されている変数を渡されたら、それを箇条書きにします。
```javascript
function bulletPoint(array) {
  var result = document.getElementById('result');
  for (i = 0; i < array.length; i++) {
    var ul = document.createElement('ul');
    ul.textContent = array[i];
    result.appendChild(ul);
  }
}
```

分割をする方の関数も、配列を返すように改変します。
```javascript
function textToTweets(txt) {
  var num = Math.ceil(txt.length/140);
  var tweets = [];                      // 追加
  for (i=0; i<num; i++) {
    var tweet = txt.substr(i*140, 140);
    tweets.append(tweet);
  }
  return tweets;
}
```

これで、alertと同じ使い心地で箇条書きができるようになりました。
```javascript
function submit() {
  var txt = document.getElementById('txt').value;
  var tweets = textToTweets(txt);
  bulletPoint(tweets);
}
```

## 2. ポスター分割
秋祭の装飾部門 兼 体育会応援部のあなたは、文字が入った、横にとても長いポスターを作らねばなりません。
ですがお金が無いのでCNSのプリンターで、A4に数文字ずつ入れて印刷していきます。
秋祭では各教室の入り口に飾りたいので、小さめに、A4に6文字ずつ印刷するとしましょう。
体育会では体育館などに飾りたいので、大きめで、A4に3文字ずつ印刷するとしましょう。
ツイート分割の問題で書いた関数を改良して、任意の文字数に柔軟に対応させてください。
引数を使うと簡単です。

## 3. シーザー暗号
とても簡単な方法で文字列を暗号化する関数を作ってみましょう。
シーザー暗号やROT13と言って、

ABCXYZ →`ROT13`→ NOPKIM

といった具合に、文字を**それぞれ**アルファベット13文字分ずらす単純なものですが、中々に読みにくくなりますね。
入力した英数字を13文字ずらす関数を書いてください。
文字列が1つ返ってくれば良いので、表示は`alert()`等で結構です。
ポスター分割の問題で書いた関数が役立つかもしれません。
ヒントとしては、[charCodeAt()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/String/charCodeAt) と [fromCharCode()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/String/fromCharCode) を使うと短く書けるので推奨ですが、方法は色々あります。

## 4. 色々なROT
最後に、ROT13の関数を改良して、13文字だけでなく任意の文字数をずらせるようにしてください。
これらの文字列が読めたら終了です。

> URW

> ijhnumjwnsl

> rkmuob-cdivo
