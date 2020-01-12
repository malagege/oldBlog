---
title: jQuery contains 可以抓大小寫的方法
date: 2020-01-11 22:55:26
tags: [jquery]
categories: JavaScript
---

最近做單頁篩選某欄位資料
使用 jQuery contains selector 可以簡單實作
但發現 contains 沒法搜尋到大小寫不同的單字
就爬了一下 Google 紀錄一下


<!--more-->

參考如下方法
* [Make jQuery :contains Case-Insensitive | CSS-Tricks](https://css-tricks.com/snippets/jquery/make-jquery-contains-case-insensitive/)
* [jquery :contains case-Insensitive - JSFiddle](https://jsfiddle.net/bipen/dyfRa/)


```js
$.extend($.expr[":"], {
"containsIN": function(elem, i, match, array) {
return (elem.textContent || elem.innerText || "").toLowerCase().indexOf((match[3] || "").toLowerCase()) >= 0;
}
});

```

可以用`$('td:containsIN("'+value+'")')` 抓到你要的資料
非常簡單