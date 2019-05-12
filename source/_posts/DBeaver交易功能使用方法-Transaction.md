---
title: DBeaver交易功能使用方法(Transaction)
date: 2019-04-20 23:27:48
tags: [dbeaver, transaction, mysql]
categories: MySQL
---

因為最近公司要測試刪除功能
但是很怕誤刪自己 localhost 資料
在想有什麼方法可以簡單測試
後來想到`交易功能`

<!--more-->

[XYZ 的筆記本: MySQL 交易功能 Transaction 整理](https://xyz.cinc.biz/2013/05/mysql-transaction.html)

在這篇能簡單看到用法
不過，我最近工作環境只裝 DBeaver
所以在想有什麼方案
最後有想到用交易功能

其實很簡單，只要連線 DB，`F3`開啟 new Script
確定上面是否連線指定`DB`

{% asset_img 1.png 提交/取消指令 %}

{% asset_img 2.png 切換auto commit/手動交易 %}

下面還有一些設定
聽說是跟 Isolation 有關係
這個我就不清楚了
有時間在了解一下，我映像中好像有些設定 db 查詢時候
db 會 lock，聽說跟這個有關係

[資料庫交易的 Isolation – getamis – Medium](https://medium.com/getamis/database-transaction-isolation-a1e448a7736e)

參考來源:
[Auto and Manual Commit Modes · dbeaver/dbeaver Wiki](https://github.com/dbeaver/dbeaver/wiki/Auto-and-Manual-Commit-Modes)
[Pending Transactions · dbeaver/dbeaver Wiki](https://github.com/dbeaver/dbeaver/wiki/Pending-Transactions)
