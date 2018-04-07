---
layout: post
title: '[CSS]看的到確摸不到的pointer-events屬性'
date: 2017-05-22 15:52
comments: true
categories: CSS
tags: [CSS,pointer-events]
---
其實這篇是網站特效筆記
但還是分開兩個，方便以後參考

<!--more-->

![](http://i.imgur.com/gSfaqHF.gif)
[http://www.tenshi-no-3p.com/](http://www.tenshi-no-3p.com/)網站看到下圖特效，往下飄的羽毛竟然放在前面
怎麼不會擋住下面的連結
經過仔細的探討
原來CSS有pointer-events好用的屬性
在此筆記:D
```css
pointer-events: none;
```
就這麼簡單
<script async src="//jsfiddle.net/aucwaogL/embed/html,css,result/"></script>

至於羽毛特效之後在分悉怎麼用的(pixi.js)
[http://blog.aquaring.co.jp/517](http://blog.aquaring.co.jp/517)

參考來源：
穿透的 div ( pointer-events )
[http://www.oxxostudio.tw/articles/201409/pointer-events.html](http://www.oxxostudio.tw/articles/201409/pointer-events.html)

pointer-events
[https://developer.mozilla.org/zh-TW/docs/Web/CSS/pointer-events?v=control](https://developer.mozilla.org/zh-TW/docs/Web/CSS/pointer-events?v=control)