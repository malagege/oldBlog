---
title: MySQL查詢進行中程序狀況，並刪掉lock程序
date: 2018-05-17 20:20:12
tags: [MySQL,sql,lock]
categories: MYSQL
---


因為網域關係，平常都是使用phpmyadmin預做設定
有時候下比較差的SQL，就不能執行中斷指令
phpmyadmin就會卡住....
剛好發現用cli模式登入mysql
可以做刪除程序
<!--more-->

```
mysql -h ip -u xxxx -p
```
就可以登入mysql資料庫裡面
只要在不打`;`下
無法送出指令

```
show processlist;
```
可以看到是否有lock程序

使用`kill`可以做刪除動作
```
kill id ;
```

發現在不用phpmyadmin時候，cli可以中斷程序
phpmyadmin送出去，就~~像變了心女朋友一樣回不來了~~不能中斷
只是無法修改是一行打做的東西


[MySQL 常用指令 - Jex’s Note](http://blog.jex.tw/blog/2013/06/03/mysql-command/)
[學習備忘錄: 安裝與登入mysql](http://xiangyang17spc.blogspot.tw/2008/11/mysql.html)
