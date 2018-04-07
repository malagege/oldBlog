---
layout: post
title: '[JavaScript]閉包筆記記錄'
date: 2016-10-02 14:04
comments: true
categories: JavaScript
tags: [閉包,JavaScript,reference]
---
之前看JavaScript閉包文章都看不懂，前陣子買了FB社群推薦的「忍者JavaScript開發技巧探秘」
最近先跳著看到閉包單元，有了解一點東西跟以前錯誤的觀念，就先來這邊做一下筆記。
免得之後全部忘光光了XD

<!--more-->

首先，前面介紹每個function都是閉包(我驚呆了)
我這邊就先舉例範例
```js
var value = 'hello world'
function a1(){
    console.log(value)  // print hello world
}
```
相信大家都知道這一定會顯示hello world吧
但我覺得大家都會不太清楚這是閉包
以下是我記錯當時錯誤觀念
> 前陣子，跟同事討論這個問題，他認為JavaScript在function外面就是全域變數
所以值也會抓到。
我認為是因為當function沒有宣告變數，會自動抓取window.value
不過跟我同事一樣，也是全域變數觀念。

以前有看到在瀏覽器沒有下var變數，變數會寫在window物件上
我以為同理可證跟function一模一樣，但測試結果也是這樣

以前，看JavaScript大全完全看不懂閉包這單元
今天終於開竅了。

**簡單來講閉包就是function抓取變數，先從function內抓取變數，假如function內沒變數，會從外面抓取變數**
他有著call by reference觀念
詳細：http://eddychang.me/blog/javascript/84-js-clousure.html
上面網站圖片和內容寫得還滿清楚的

好了，再來進入番外篇
JavaScript怎麼會有call by reference
我一直以為JavaScript都是call by value
好險，看到上面這個文章
JavaScript有一個形態是call by reference
就是Object物件就是call by reference

```
var obj = {a:1}
function a(value){
    value.a=3;
}
a(obj);
console.log(obj);   // print Object  {a:3}
```
以前好在沒有採到這個雷，下次應該不會踩到XD

然後，接下來突然想到以前看不懂的閉包[文章](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures#Creating_closures_in_loops_A_common_mistake)
```html
<p id="help">Helpful notes will appear here</p>
<p>E-mail: <input type="text" id="email" name="email"></p>
<p>Name: <input type="text" id="name" name="name"></p>
<p>Age: <input type="text" id="age" name="age"></p>
```

```js
function showHelp(help) {
  document.getElementById('help').innerHTML = help;
}

function setupHelp() {
  var helpText = [
      {'id': 'email', 'help': 'Your e-mail address'},
      {'id': 'name', 'help': 'Your full name'},
      {'id': 'age', 'help': 'Your age (you must be over 16)'}
    ];

  for (var i = 0; i < helpText.length; i++) {
    var item = helpText[i];
    document.getElementById(item.id).onfocus = function() {
      showHelp(item.help);
    }
  }
}

setupHelp();
```
這下能理解item.help為什麼抓到都是helpText最後面一個
因為每個迴圈實做閉包
但是setupHelp()跑完item變數就會存在於閉包，不會消失(但外在function沒法存取這個變數)
所以每個onfocus都會跑出`Your age (you must be over 16`
了解過去不了解的東西，可喜可賀^o^

再來，番外篇2
[文章](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures#Creating_closures_in_loops_A_common_mistake)上面有寫let可以解決閉包範例
所以就爬文一下
>`let`好像類似Java變數生活周期是在{}結束

*上述只是猜測*
```js
var a = 2;
if(a==2){
   let a = 1
   console.log(a);  // print 1
}
 console.log(a);  // print 2
 ```
有空深入了解

聽說閉包是JavaScript精華，目前提到的東西可能還算閉包最基礎的東西
像scope chain、匿名函式......都還沒研究。
等了解後再記錄筆記吧

參考：
《你不知道的JavaScript》整理（一）——作用域、提升與閉包 - 每日頭條
https://kknews.cc/other/6bmzqq.html
闭包 - JavaScript | MDN
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures#Creating_closures_in_loops_A_common_mistake
Javascript 閉包 Closure « Guang's Note
http://guang.logdown.com/posts/277383-javascript-closure
JavaScript筆記: function is call by "value" or call by "reference"? « 弱弱前端碎碎念
http://heyyou.logdown.com/posts/145054
Tag : javascript « 弱弱前端碎碎念
http://heyyou.logdown.com/tags/javascript
從ES6開始的JavaScript學習生活 · GitBook
https://www.gitbook.com/book/eyesofkids/javascript-start-from-es6/details
refer
http://eddychang.me/blog/javascript/84-js-clousure.html
深入理解什麼是javascript中的閉包。_StackDoc
http://fanli7.net/a/caozuoxitong/OS/20110830/125313.html
let
https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Statements/let#let_%E7%9A%84%E4%BD%9C%E7%94%A8%E5%8D%80%E5%A1%8A