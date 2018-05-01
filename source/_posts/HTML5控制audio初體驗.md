---
title: HTML5控制audio初體驗
date: 2018-05-01 19:05:24
tags: [audio, HTML]
categories: HTML
---

平常沒什麼在用audio
最近專案有用到，就簡單來記錄一下


<!--more-->
```html
<audio>
```

使用時候，不會顯示出頁面上
預設也不會播音樂
`autoplay`會自動播放
`controls`會顯示出來頁面
`loop`循環播放
`preload`有三種載入狀態
 * `auto`頁面載入時侯會把檔案載入進來
 * `metadata` 頁面載入時侯會把檔案載入進來
 * `none`頁面載入時候不會把資料載入進來

`muted`靜音

還有很常看到使用`socure`搭配，假如多個音源檔(例如:ogg和mp3)
可以使用`socure`
不過看[HTML source Tag](https://www.w3schools.com/tags/tag_source.asp)
竟然還可以用在picture上面
相信做rwd可能會比較方便
不用寫jsXD
有空來筆記
