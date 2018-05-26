---
title: audio和video在手機上不能autoplay問題
date: 2018-05-13 21:07:47
tags: [HTML,mp4,audio,video,autoplay]
categories: HTML
---

最近做專案都用到audio autoplay，但發現手機chrome不能自動播放，但是Firefox
這根手機瀏覽器設定有關係，現在像chrome預設都是用 媒體 自動關閉播放
我去設定看Firefox播放...，難怪在播放音樂清單網頁，Firefox在螢幕關閉
音樂還可以繼續放，但Chrome就不行了...，現在大概知道應該就是這個原因
<!--more-->
解決方法，就是手機要做一個按鈕給使用者去做播放
這邊有簡單用了，position:absoulte置中方法做一個東西
http://jsbin.com/pejuvakuko/1/edit?html,css,output
```javascript=
$('svg').click(function(){
    audio.play();
    $('svg').hide();
})
```
所以大概簡單做了一下這個
參照最近學會的absolute置中方法
### loading
https://jsbin.com/diyavuyihi/1/edit?html,css,output



下次做這個東西，希望別再踩到雷

其實，手機瀏覽器可以讓使用者設定
但預設幾乎都是關閉的



[Chrome 66讓數百萬款網頁遊戲消音，Google緊急調整自動播放政策 | iThome](https://www.ithome.com.tw/news/123231)