---
title: Some answers in Javascript
date: 2018-10-21 19:16:22
categories:
tags: javascript
---

## 1. ツイート分割
```javascript
function textToTweets(txt) {
  var num = Math.ceil(txt.length/140);
  var tweets = [];
  for (i=0; i<num; i++) {
    var tweet = txt.substr(i*140, 140);
    tweets.push(tweet);
  }
  return tweets;
}

function bulletPoint(array) {
  var result = document.getElementById('result');
  for (i = 0; i < array.length; i++) {
    var ul = document.createElement('ul');
    ul.textContent = array[i];
    result.appendChild(ul);
  }
}

function submit() {
  var txt = document.getElementById('txt').value;
  var tweets = textToTweets(txt);
  bulletPoint(tweets);
}
```

## 2. ポスター分割
```javascript
function splitText(txt, n) {
  var num = Math.ceil(txt.length/n);
  var arr = [];
  for (i=0; i<num; i++) {
    var sub = txt.substr(i*n, n);
    arr.push(sub);
  }
  return arr;
}
```

## 3. シーザー暗号
```javascript
var CHAR_A = 65;
var CHAR_Z = 90;

function rot13(txt){
  txt = txt.toUpperCase();
  var arr = splitText(txt, 1);
  for (i=0; i<arr.length; i++) {
    var ascii = arr[i].charCodeAt(0);
    if (ascii < CHAR_A || ascii > CHAR_Z) {
      arr[i] = String.fromCharCode(ascii);
    } else if (ascii + 13 <= CHAR_Z) {
      arr[i] = String.fromCharCode(ascii + 13);
    } else {
      arr[i] = String.fromCharCode(ascii - 13);
    }
  }
  return arr.join('');
}
```

## 4. 色々なROT
```javascript
function rot(txt, n){
  txt = txt.toUpperCase();
  var arr = splitText(txt, 1);
  for (i=0; i<arr.length; i++) {
    var ascii = arr[i].charCodeAt(0);
    if (ascii < CHAR_A || ascii > CHAR_Z) {
      arr[i] = String.fromCharCode(ascii);
    } else if (ascii + n <= CHAR_Z) {
      arr[i] = String.fromCharCode(ascii + n);
    } else {
      arr[i] = String.fromCharCode(ascii - 26 + n);
    }
  }
  return arr.join('');
}
```

> ROT
(ROT23)

> deciphering
(ROT21)

> hacker-style
(ROT16)
