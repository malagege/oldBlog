---
layout: post
title: '瀏覽器播放影片控制開始時間和結束時間'
date: 2014-06-25 08:28
comments: true
categories: HTML
tags: [mp4,video]
---
最近在想有沒有瀏覽器播放MP4控制播放時間
結果一搜尋還真的有耶!!!

在(Start HTML5 video at a particular position when loading?)[http://stackoverflow.com/questions/5981427/start-html5-video-at-a-particular-position-when-loading]這篇找到相關方法
<!--more-->

```
http://www.yoursite.com/yourfolder/yourfile.mp4#t=5,15
```

後來還發現能用00:00:00格式
```
http://www.yoursite.com/yourfolder/yourfile.mp4#t=0:00:00,0:01:00
```


(Media Fragment)[http://www.w3.org/TR/media-frags/]