---
layout: post
title: '利用影片來做背景動態效果'
date: 2014-07-06 20:24
comments: true
categories: 網頁特效
tags: [z-index,position]
---
最近逛到[未確認進行式動畫官網](http://mikakunin.jp/index.html)
看到背景是動態畫面，第一個直覺這是`Flash`
但右鍵不是!!!!
奇怪就算是HTML5播放影片右鍵應該可以控制影片
<!--more-->

簡單看了一下原始碼
是用HTML5裡的Video來播放了，但怎麼沒辦法右鍵控制???
其實最外面的那一層**#headWrap**把Video擋住了

其實div還用滿多z-index圖層

目前搞不懂#headWrap的z-index:20
Video的z-index:40
\#headWrap可以在Video上面

最後，找到[z-index playground](http://www.css-101.org/z-index/playground.php)
雖然左邊英文看不懂orz，但是簡單玩了一下右邊圖層關係
`某div裡的div的z-index`跟外面div裡的z-index比較
會對照`某div裡的div的z-index`的parent div  z-index去跟外面比較
而非div裡面的z-index
___此說法可能有錯誤___

所以[未確認進行式動畫官網](http://mikakunin.jp/index.html)
\#headWrap的z-index:20
雖然Video的z-index:40，但是外層的#visualWrap的z-index:10
所以\#headWrap就在上面

雖然有找到[CSS z-index 屬性的使用方法和層級樹的概念](http://www.neoease.com/css-z-index-property-and-layering-tree/)
但親身玩一下[z-index playground](http://www.css-101.org/z-index/playground.php)才會比較清楚

------

話說看暗殺教室和未確認進行式官網的CSS寫法
感覺網頁設計師是用SASS寫出來的0.0
可惜目前CSS能力不足，可能沒辦法碰此部份orz