---
title: HTML5 History API 筆記
date: 2021-07-24 14:53:32
tags: [html5,history]
categories: HTML
---


參考:[操控瀏覽器歷史紀錄 - Web APIs | MDN](https://developer.mozilla.org/zh-TW/docs/Web/API/History_API)

### 上/下一頁API

> 往前往後歷史紀錄可以用 back(), forward(), 和 go() 的方法。


```javascript=
window.history.back();
window.history.forward();
```

### 移置特定頁面

> 移動到特定的歷史紀錄
> 
> 你可以用 go() 方法來從頁面的 session history 紀錄中載入特定紀錄，以目前頁面的相對位置而定（目前的頁面想當然爾是 index 0）。

```javascript=
window.history.go(-1);
window.history.go(1);

```

### 其它

```javascript=
var numberOfEntries = window.history.length;
```

## 加入和修改歷史紀錄


### pushState() 方法範例

```javascript=
var stateObj = { foo: "bar" };
history.pushState(stateObj, "page 2", "bar.html");

```


### replaceState() 方法

```
var stateObj = { foo: "bar" };
history.replaceState(stateObj, "page 2", "bar.html");
```


### popstate 事件

```javascript=
window.onpopstate = function(event) {
  alert("location: " + document.location + ", state: " + JSON.stringify(event.state));
};

history.pushState({page: 1}, "title 1", "?page=1");
history.pushState({page: 2}, "title 2", "?page=2");
history.replaceState({page: 3}, "title 3", "?page=3");
history.back(); // alerts "location: http://example.com/example.html?page=1, state: {"page":1}"
history.back(); // alerts "location: http://example.com/example.html, state: null
history.go(2);  // alerts "location: http://example.com/example.html?page=3, state: {"page":3}
```

這邊可以看到，第一頁面，state是`null`，replaceState跟 location.replace 很像，上下一頁不會取代掉



## 小記

一開始我以為 HTML5 History API 很難，但是看 `popstate 事件`，我就恍然大悟，大概知道要怎麼使用。簡單來說跟`location.href` 很像，但可以帶 `state`和 改變 URL。透過`popstate 事件` 去操作畫面。

這邊看到還滿有趣的範例:[重新認識 JavaScript 番外篇 (2) - 你所不知道的 pushState - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10195793)