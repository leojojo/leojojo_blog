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
    tweets.append(tweet);
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
    arr.append(sub);
  }
  return arr;
}
```

## 3. シーザー暗号
```javascript
function rot13(txt){
  var arr = splitText(txt, 1);
  for (i=0; i<arr.length; i++) {
    var ascii = arr[i].charCodeAt(0);
    arr[i] = String.fromCharCode(ascii + 13);
  }
  return arr.join();
}
```

## 4. 色々なROT
```javascript
function rot(txt, n){
  var arr = splitText(txt, 1);
  for (i=0; i<arr.length; i++) {
    var ascii = arr[i].charCodeAt(0);
    arr[i] = String.fromCharCode(ascii + n);
  }
  return arr.join();
}
```

> ROT

> deciphering

> hacker-style
