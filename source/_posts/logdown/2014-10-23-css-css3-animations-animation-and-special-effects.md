---
layout: post
title: '[CSS] CSS3 Animations動畫特效'
date: 2014-10-23 03:08
comments: true
categories: CSS
tags: [CSS,animation]
---
最近我同學問我這個[背景移動特效](http://yesdesigning.com/forum-63-1.html)要怎麼用
他以為是JQuery寫出來的
結果我找了老半天，原來是CSS3

`animation:160s linear 0s normal none infinite animatedBackground`

```css
name {
    animation: name duration timing-function delay iteration-count direction fill-mode play-state;
}
```
<!--more-->

※簡單筆記
name-> @keyframe名稱
duration -> 設定幾秒內跑完
timing-function -> 開始到結束的速度
delay -> 動畫開始前延遲幾秒
iteration-count -> 重複執行幾次
direction -> 執行完動畫後，接下來的動作模式
fill-mode -> 動畫跑完後，該此物件擺設位置
play-state -> 動畫狀態暫停

@keyframes

```css
/* Chrome, Safari, Opera */
@-webkit-keyframes mymove {
    from {top: 0px;}
    to {top: 200px;}
}

/* Standard syntax */
@keyframes mymove {
    from {top: 0px;}
    to {top: 200px;}
}
```

```css
/* Chrome, Safari, Opera */
@-webkit-keyframes mymove {
    0%   {top: 0px; left: 0px; background: red;}
    25%  {top: 0px; left: 100px; background: blue;}
    50%  {top: 100px; left: 100px; background: yellow;}
    75%  {top: 100px; left: 0px; background: green;}
    100% {top: 0px; left: 0px; background: red;}
}

/* Standard syntax */
@keyframes mymove {
    0%   {top: 0px; left: 0px; background: red;}
    25%  {top: 0px; left: 100px; background: blue;}
    50%  {top: 100px; left: 100px; background: yellow;}
    75%  {top: 100px; left: 0px; background: green;}
    100% {top: 0px; left: 0px; background: red;}
}
```
參考來源：
http://yesdesigning.com/forum-63-1.html(特效)
http://www.w3schools.com/cssref/css3_pr_animation-keyframes.asp