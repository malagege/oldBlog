---
title: codeigniter設定router踩雷
date: 2019-05-03 22:07:42
tags: [codeigniter]
categories: PHP
---

今天設定 Codeigniter 路由的時候
發現怎麼不能正常倒到 Default_controller
後來發現不能設定 **Contoller 第二層資料夾!!!**
好了，結束 XD

<!--more-->

最後，不太想動到原本路徑程式，
所以 Welcome.php 做`inlcude application\contoller\ooo\xxx.php`繼承(extends)
完成!!!!

**注意：設邊由於所有 index.php 關係，所以 include 要由底層資料夾開始**

其他小記:
怕沒有時間記錄，小記在這邊

- [Git webhook 实现自动部署教程](http://blog.text.wiki/2017/10/31/git-webhooks-for-php.html)
- [Windows 系统中的 Linux—WSL 安装及简单使用-水幕青辰的网络笔记-51CTO 博客](https://blog.51cto.com/3chou/2294231)
