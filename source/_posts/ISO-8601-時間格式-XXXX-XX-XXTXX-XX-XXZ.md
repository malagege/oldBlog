---
title: "ISO 8601 時間格式 XXXX-XX-XXTXX:XX:XXZ"
date: 2019-08-14 22:32:07
tags: [timezone]
categories: 程式心法
---

之前有看到這個時間格式
但一直沒有紀錄
最近剛好看到這個時間格式
筆記筆記

<!--more-->

一開始的時候，我不知道這個時間格式是什麼名稱`ISO 8601`
剛好看到 php 官網[PHP: DateTime - Manual](https://www.php.net/manual/en/class.datetime.php)
`const string DateTimeInterface::ISO8601 = "Y-m-d\TH:i:sO"`
所以才找到名稱...

有爬到這一篇詳細記錄[关于“时间”的一次探索 - 前端涨姿势 - SegmentFault 思否](https://segmentfault.com/a/1190000004292140) {% asset_link 備份圖 web1.png %}

因為 MySQL 不能使用 ISO 8601 儲存
所以在使用 Laravel 能不能搭配 Carbon
還需要研究，有空實作在補
