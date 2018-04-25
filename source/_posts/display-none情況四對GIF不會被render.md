---
title: 'display:none情況四對GIF不會被render'
date: 2018-04-25 19:46:12
tags: [display,CSS,gif,render,HTML,image]
categories: CSS
---

今天看到了gif在`display:block` 竟然動畫才開始動
查了一下下，原來這跟render有關係
在此做一下筆記...

<!--more-->

>"display:none" is Your Friend
If you use display:none on the GIF's html <img> tag, the GIF will not be rendered at all, and will not use any CPU or GPU resources. See this and this for explanations. 
[html - Does hiding an animated GIF with CSS conserve browser resources? - Stack Overflow](https://stackoverflow.com/questions/33762652/does-hiding-an-animated-gif-with-css-conserve-browser-resources)

>display:none; elements are not in the render tree all, so they will perform better at face value.
https://stackoverflow.com/a/11757103
[How Browsers Work: Behind the scenes of modern web browsers - HTML5 Rocks](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/#Render_tree_construction)
[前端新手村 都市傳說之隱藏元素的手法 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10195447?sc=iThelpR)


有在[javascript - Proper way to reset a GIF animation with display:none on Chrome - Stack Overflow](https://stackoverflow.com/questions/10730212/proper-way-to-reset-a-gif-animation-with-displaynone-on-chrome)
看到使用[JSFiddle](http://jsfiddle.net/ult_combo/F8Q44/3/)Firefox,IE都會重置
但是CHROME不會...
有在裡面文章找到解法https://stackoverflow.com/a/21104927
[http://jsfiddle.net/F8Q44/19/](http://jsfiddle.net/F8Q44/19/)
```js
$('img').show().each(function() {
    this.offsetHeight;
}).prop('src', 'image src');
```

這個問題好像是我之前寫的render問題解決方法...
[[JavaScript] 網頁渲染的過程 (rendering pipline) « 進擊的程式新手](http://malagege.logdown.com/posts/1731337-javascript-web-page-rendering-rendering-pipline)


常使用`prop`用在checked上面，不過今天看到這個用法
查看了一下官網文件
>It should be used when setting selectedIndex, tagName, nodeName, nodeType, ownerDocument, defaultChecked, or defaultSelected. Since jQuery 1.6, these properties can no longer be set with the .attr() method. They do not have corresponding attributes and are only properties.

有些以前沒辦法控制selectedIndex, tagName, nodeName, nodeType, ownerDocument, defaultChecked, or defaultSelected.這些東西
不過現在jQuery可以用prop抓到資訊

[javascript - jquery .prop('defaultSelected') seems to malfunction in IE9 - Stack Overflow](https://stackoverflow.com/questions/19932965/jquery-propdefaultselected-seems-to-malfunction-in-ie9)

```html
<form action="">
  <input type="checkbox" name="" id="" checked>
  <input type="reset" value="test">
</form>
```
><input> elements of type "reset"  are rendered as buttons, with a default click event handler that resets all of the inputs in the form to their **initial values**.
很多網站教清空表單
但其實正確要說還原表單預設值
(怎麼會寫到這個，因為玩到checkbox,defaultSelected就突然想到reset問題，結果不出所料)


結論沒事還是不要用gif做倒數...