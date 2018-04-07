---
layout: post
title: '[JavaScript] 網頁渲染的過程 (rendering pipline)'
date: 2017-04-21 12:48
comments: true
categories: JavaScript
tags: [JavaScript,網頁渲染,rendering pipline]
---
前幾天在hahow上 [動畫互動網頁程式入門 (HTML/CSS/JS)](https://hahow.in/courses/56189df9df7b3d0b005c6639)
這沒有要打廣告，不過老師真的教得不錯
看到講師上到實作購物車時候
其中某一個function利用多個(json)物件組出list清單
而新增、刪除都是對物件操作，再透過function把list清單清空再繪製

！！！！這看起來有問題，講師怎麼這樣做，這項目很多時，捲軸會有問題，這一定有bug
<!--more-->

不過還是簡單做一個小實驗好了，

http://jsbin.com/fucoxur/edit?html,css,js,console,output
結果
![](https://scontent-tpe1-1.xx.fbcdn.net/v/t31.0-8/17917730_1726055847411527_4438982537059638344_o.jpg?oh=659fd51996f499d51f9650ab894b273a&oe=5997A193)

怎麼想都想不到原因，就求救FB社群大大求救
https://www.facebook.com/groups/f2e.tw/permalink/1271697796200916/

  >  原本網頁渲染的過程 (rendering pipline) 會先執行 js 然後看 dom 跟 cssom 做成 render tree 然後畫在畫面上，你原先的作法等於 dom 被修改了兩次之後瀏覽器才開始跑剩餘的 rendering pipline 的流程 所以會依最後一次的結果 dom 的結果呈現
然而你讀取 scrolltop 的屬性的時候 網頁會 reflow 算出 scrolltop 的值，這時瀏覽器就會被強迫進行一次繪製，就會有你原本預期的行為

reflow不知道還有什麼情境，還能運用這個技巧，先記個筆記，怕忘記XD


參考來源
https://developers.google.com/web/fundamentals/performance/rendering/
https://gist.github.com/paulirish/5d52fb081b3570c81e3a
inspiregate.windspeaker.co/post/3959-the-browser-39-s-rendering-principle

