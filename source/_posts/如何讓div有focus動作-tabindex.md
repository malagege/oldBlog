---
title: 如何讓div有focus動作(tabindex)
date: 2019-02-27 23:34:15
tags: [tabindex, html]
categories: HTML
---

正常 focus 只有 input,textarea,button 等等
平常 div 是沒有 focus，但最近看`focus-within`有一個案例
[神奇的选择器 :focus-within - WEB 前端 - 伯乐在线](http://web.jobbole.com/95025/)
[Off-screen nav with :focus-within [PURE CSS]](https://codepen.io/dannievinther/pen/NvZjvz)
查了一下是 tabindex 關係
由於這個很少用，所以還是記錄一下。

<!--more-->

[说说 tabindex 的那些事儿 | bubkoo](http://bubkoo.com/2015/02/01/using-the-tabindex-attribute/) {% asset_link web.png 備份圖 %}

> 定義和用法
> tabindex 屬性規定元素的 tab 鍵控制次序（當 tab 鍵用於導航時）。
> 提示和註釋
> 註釋：以下元素支持 tabindex 屬性：`<a>, <area>, <button>, <input>, <object>, <select> 以及 <textarea>`。

[HTML tabindex 属性](http://www.w3school.com.cn/tags/att_standard_tabindex.asp)
[(译)如何让 div 可以有 focus 事件?](https://www.douban.com/note/238463483/)
div 或 span 設定 tabindex 就會有 focus

最後剛好最近在查有沒有按鍵盤切換方向鍵移動不同 table 的 input
剛好有看到
[garygreen/arrow-table: Navigate HTML tables with arrow keys (jQuery plugin)](https://github.com/garygreen/arrow-table)
[jQuery-利用上下左右鍵移動輸入焦點-黑暗執行緒](https://blog.darkthread.net/blog/jquery-arrow-keys-focus-moving)
由於是上面是用 tabindex 去實作

真想把他改成文字用左再調整更換 input 位置
[javascript 对象之 selectionStart selectionEnd - 选择了就坚持 - 博客园](https://www.cnblogs.com/perseverancevictory/p/3665814.html)
[Cross browser Javascript code to get/set caret position in textarea/input](https://www.vishalon.net/blog/javascript-getting-and-setting-caret-position-in-textarea)

```js
function getCaretPosition(ctrl) {
  // IE < 9 Support
  if (document.selection) {
    ctrl.focus();
    var range = document.selection.createRange();
    var rangelen = range.text.length;
    range.moveStart("character", -ctrl.value.length);
    var start = range.text.length - rangelen;
    return { start: start, end: start + rangelen };
  }
  // IE >=9 and other browsers
  else if (ctrl.selectionStart || ctrl.selectionStart == "0") {
    return { start: ctrl.selectionStart, end: ctrl.selectionEnd };
  } else {
    return { start: 0, end: 0 };
  }
}

function setCaretPosition(ctrl, start, end) {
  // IE >= 9 and other browsers
  if (ctrl.setSelectionRange) {
    ctrl.focus();
    ctrl.setSelectionRange(start, end);
  }
  // IE < 9
  else if (ctrl.createTextRange) {
    var range = ctrl.createTextRange();
    range.collapse(true);
    range.moveEnd("character", end);
    range.moveStart("character", start);
    range.select();
  }
}
```

先留著...以後再修改
