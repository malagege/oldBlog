---
title: JS 原生方法做到 jQuery one 事件
date: 2019-09-01 22:08:16
tags: [javascript]
categories: JavaScript
---

今天想用 jQuery one 監聽事件
想找原生簡單使用方法
原本想 Event 直接取消監聽
但還是有爬到比較簡單的做法，簡單紀錄一下

<!--more-->

```js
document.querySelector('#click').addEventListener('click', function(e){
    if(e.currentTarget.dataset.triggered) return;
    e.currentTarget.dataset.triggered = true;
    alert('clicked');
})
```
```html
<button id="click">Click me</button>
```

參考來源:[javascript - JS equivalent for jQuery one() - Stack Overflow](https://stackoverflow.com/questions/36312249/js-equivalent-for-jquery-one)