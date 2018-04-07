---
layout: post
title: '[jQuery] :first 和:first-child 不一樣'
date: 2016-06-25 10:03
comments: true
categories: JQuery
---
由於同事有用我寫的JavaScript做tr跳頁監聽事件
由於第一欄是input radio不需做跳頁
我就簡單寫了
```js
$('table tr').click(function(){
    location.href='xxxxxx';
});

$('table tr:first-child').off('click');
```
<!--more-->

同事問我怎麼不用:first，怎麼使用`:first-child`
他說他改效果一樣
```
$('table tr:first').off('click');
```
~~瞬間被考倒~~ 我忘了他們的差別
當然馬上Google腦補一下
才發現他們不一樣....

:first只會select第一個
:first-child只會select每個父層的第一個子元素

`:first-child`等於`:nth-child(1)`

還有`:first-of-type`和`:nth-of-type(1)`還是有差
還有`:nth-last-child(n)`
有空在記一下筆記

備注:CSS沒有`:first`
