---
title: Laravel 新手實作 Vue.js 簡單使用方法 和 yield 應用
date: 2019-06-05 00:16:45
tags: [php, laravel]
categories: Laravel
---

今天去聽 Laravel 和 Vue.js 研討會
身為學一點 Vue.js 根想學 Laravel
這場真的還滿適合我的
就報名!!
當天回去就寫這篇

<!--more-->

首先，今天講了很多 Laravel 的 balde 應用
講者從 View 的 balde 開始講
特別獎到 `yield` 預留一個空位意思
之前有一些程式會有這個關鍵字，都沒去了解(跪鍵盤

可以套用 section 進去，當場示範完成 [LaraGirls|專屬女性的程式學習社群](https://www.laragirls.org/) 官網
首先，他找 Boostrap 相似版面完成 HTML 排版
然後 router , view (搭配 yield, extends ,section, foreach)
一小時內完成網站排版(mockup)

講者大大還說，透過這個可以做出簡單的 mockup
在跟使用者談需求的時候
非常方便!!!!

接下來就是 Vue.js

1. vue.js 已經包到 Laravel，之接呼叫 component 就可以了(首先載入 app.js 跟 設定 id=app)
2. 前後端分離

有空詳細寫

這個跟 Codeigniter 比起來 View 地方差很多
不過 header 選單如何做 active 部份
有 google 查了一下

- [php - Setting Bootstrap navbar active class in Laravel 5 - Stack Overflow](https://stackoverflow.com/questions/29837555/setting-bootstrap-navbar-active-class-in-laravel-5)
- [Laravel: How to Make Menu Item Active by URL/Route – Quick Admin Panel](https://quickadminpanel.com/blog/laravel-how-to-make-menu-item-active-by-urlroute/)
- [lavary/laravel-menu: A quick way to create menus in Laravel 5](https://github.com/lavary/laravel-menu)

研討會投影片和程式內容

- [在 Laravel 裡玩切版](https://www.slideshare.net/shengyou/layout-with-blade-and-vue/)
  [laravel-dojo/20190604-laragirls-layout-with-blade-and-vue-1: 受邀至 LaraGirls 示範如何使用在 Laravel 裡以三種不同的方式做切版開發。此為現場實作的程式碼範例裡，Laravel 的部份。](https://github.com/laravel-dojo/20190604-laragirls-layout-with-blade-and-vue-1)
- [laravel-dojo/20190604-laragirls-layout-with-blade-and-vue-2: 受邀至 LaraGirls 示範如何使用在 Laravel 裡以三種不同的方式做切版開發。此為現場實作的程式碼範例裡，Vue 的部份。](https://github.com/laravel-dojo/20190604-laragirls-layout-with-blade-and-vue-2)
