---
title: JavaScript 效能優化 Debounce 和 Throttle 小記
date: 2019-07-05 00:12:22
tags:
categories:
---

之前在看 Vue 範例就看到有這個 Debounce 函式運用
就很想做這個筆記
不過，今天同事有一個同時間觸發同一個 Event
最後想起一個 workaround 解決方式

<!--more-->

首先，這個一般可以用 jQuery off 方式關掉全部的 Event
但這個也不是萬靈丹
在某種情況下還是無法用
這時候想到這個也能解決這個問題

不過因為他那個按鈕(開關套件)，不能同時按，因為有做 AJAX 關係
所以要用 ajax disabled 那個物件，completed 可以 enable
就能防止 bug
但能不要用這種方式最好

首先，記錄兩個差異
Debounce 主要用在事件處發，當下點下去第一下才會處發，後面幾毫秒不會觸發
Thtrottle 主要用在多次刷新，但是不需要太頻繁，通常用在做動畫上面

## Debounce

```js
function debounce(func, delay) {
  var timer = null;
  return function() {
    var context = this;
    var args = arguments;
    clearTimeout(timer);
    timer = setTimeout(function() {
      func.apply(context, args);
    }, delay);
  };
}
```

簡單講就是設定

## Throttle

```js
function throttle(func, threshhold) {
  var last, timer;
  if (threshhold) threshhold = 250;
  return function() {
    var context = this;
    var args = arguments;
    var now = +new Date();
    if (last && now < last + threshhold) {
      clearTimeout(timer);
      timer = setTimeout(function() {
        last = now;
        func.apply(context, args);
      }, threshhold);
    } else {
      last = now;
      fn.apply(context, args);
    }
  };
}
```

[網頁 DOM 事件的效能優化：Debounce 和 Throttle | Mr. Opengate](https://mropengate.blogspot.com/2017/12/dom-debounce-throttle.html) {% asset_link 備份圖 web1.png %}
[JS 专题之节流函数 - 知乎](https://zhuanlan.zhihu.com/p/52249747)

## 第三方套件 Debounce、Throttle

### Lodash

推薦使用這個!!
https://lodash.com/

### Underscore

http://underscorejs.org/
