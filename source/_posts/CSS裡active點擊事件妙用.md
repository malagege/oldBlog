---
title: 'CSS:active點擊事件妙用'
date: 2018-04-22 01:41:37
tags: [CSS, :active]
categories: CSS
---

一般我們學CSS很常看到:link,:active,:visited,:hover這幾個很常用在`a`
不過今天我看到一個範例
是用:active對`div`做點擊換背景事件!!!
沒想到竟然可以對`div`做變化
一般很常用mousedown,mouseup做處理
這樣可以用CSS一次解決寫兩個JavaScript
趕快來筆記一下

<!--more-->

`:active`除了對div會有影響
看我實作測試
http://jsbin.com/facifuvina/edit?html,css,js,output
點擊div時候，其實`body:active`也會跟著變色
所以:active點子子層的時候也會跟著影響父層
所以使用也要了解這個問題！

試著看API能不能看到一些`:active`資訊

>An element is said to be in a formal activation state between the time the user begins to indicate an intent to trigger the element's activation behavior and either the time the user stops indicating an intent to trigger the element's activation behavior, or the time the element's activation behavior has finished running, which ever comes first.

>An element is said to be being actively pointed at while the user indicates the element using a pointing device while that pointing device is in the "down" state (e.g. for a mouse, between the time the mouse button is pressed and the time it is depressed; for a finger in a multitouch environment, while the finger is touching the display surface).

[HTML Standard](https://html.spec.whatwg.org/multipage/semantics-other.html#in-a-formal-activation-state)
不知道是不是這個內容關係

不過在[:active | CSS-Tricks](https://css-tricks.com/almanac/selectors/a/active/)這個網站有找到所有element都可以這樣用
然後看一下支援表，沒想到支援度這麼高
以前就有這個東西，都不知道orz


>這個樣式可能會被後聲明的其他鏈接相關的偽類覆蓋，這些偽類包括 :link，:hover和 :visited。為了正常加上樣式，需要把 :active的樣式放在所有鏈接相關的樣式後，這種鏈接偽類先後順序被稱為LVHA順序: :link — :visited — :hover — :active。

>注意: 在有多鍵鼠標的系統中，CSS 3 規定 :active 偽類必須只匹配主按鍵；對於右手操作鼠標來說就是左鍵。

[:active - CSS：層疊樣式表 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:active)

改天來記錄LVHA順序筆記吧!!

參考來源：
[:active - CSS: Cascading Style Sheets | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/:active)
[HTML Standard](https://html.spec.whatwg.org/multipage/semantics-other.html#selector-active)
[:active | CSS-Tricks](https://css-tricks.com/almanac/selectors/a/active/)