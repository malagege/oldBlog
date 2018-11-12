---
title: flex 重直置中 內容大於畫面，上面被截掉解決方法
date: 2018-10-25 22:58:08
tags: [flex,css]
categories: CSS
---


之前寫程式發現用flex垂直至中超出畫面會有問題
一直以為都是用flex這個缺點不能用在大畫面上面
不過今天網路上找到兩個答案
簡單紀錄

<!--more-->

##  unsafe

justify-content: safe center
align-self: safe center

目前只有Firefox 63支援
最近才到63XD


## 子項目家margin:auto


很神奇的margin


可以直接用開發者工具去改[CSS - flexbox](http://zh-tw.learnlayout.com/flexbox.html)
有點懶截圖


參考來源:
[html - Can't scroll to top of flex item that is overflowing container - Stack Overflow](https://stackoverflow.com/questions/33454533/cant-scroll-to-top-of-flex-item-that-is-overflowing-container)