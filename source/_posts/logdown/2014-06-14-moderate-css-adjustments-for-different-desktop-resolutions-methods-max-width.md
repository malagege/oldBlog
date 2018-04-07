---
layout: post
title: '不同桌面解析度用CSS調整圖適中的方法(max-width)'
date: 2014-06-14 21:05
comments: true
categories: CSS
tags: max-width,CSS
---
今天用平板電腦看[Glayer](http://www.glayer.net/)
發現橫向、直向看某圖大小非常不一樣。
起先我以為是我的錯覺，在經過用Firefox確實大小不一樣。
<!--more-->


![畫面擷取於 2014-06-01 19.30.51.png](http://user-image.logdown.io/user/8284/blog/8171/post/206142/1EsrMUkrSI2yQ0fuBjQo_%E7%95%AB%E9%9D%A2%E6%93%B7%E5%8F%96%E6%96%BC%202014-06-01%2019.30.51.png)


![畫面擷取於 2014-06-01 19.31.24.png](http://user-image.logdown.io/user/8284/blog/8171/post/206142/wLWdhaoPQMCFxmDZHsgU_%E7%95%AB%E9%9D%A2%E6%93%B7%E5%8F%96%E6%96%BC%202014-06-01%2019.31.24.png)

本來在想是用什麼巫術才能變成這樣，Bootstrap?HTML?
沒想到是CSS的`max-width`的關係

之前看到CSS的`max-width`一直都不知道要試用於哪裡
今天總算知道了:D