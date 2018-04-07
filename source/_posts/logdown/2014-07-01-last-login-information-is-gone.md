---
layout: post
title: 'last登入資訊不見了!!!'
date: 2014-07-01 10:17
comments: true
categories: Linux
tags: [Linux,last]
---
今天在宿舍連家裡的動物機連不進去，後來就用真實IP連線去了
登入後馬上下意識用`last`去看一下登入狀況
結果發現只有一條剛剛登入的訊息!!!
<!--more-->

後來看了一下鳥哥[一些常見指令： last, lastlog, dmesg](http://linux.vbird.org/linux_basic/0570syslog/0570syslog-fc4.php#analyze_command)
```sh
[root@linux ~]# last -n number
[root@linux ~]# last -f filename
參數：
-n  ：我們知道 last 會讀出這個月的資料，若資料量太大，可使用 -n 來嚴格要求
      所以顯示的筆數即可。例如 20 筆資料： last -n 20 或 last -20 均可。
-f  ：last 預設讀出 /var/log/wtmp 這個檔案，但是我們可以透過 -f 讀取不同的
      登錄檔資訊喔！
範例：

範例一：將上個月的資料讀出，僅讀出 5 筆資料時？
[root@linux ~]# last -n 5 -f /var/log/wtmp.1
dmtsai2  pts/2             Mon Oct 24 14:18 - 14:18  (00:00)
dmtsai2  work:0 work       Mon Oct 24 14:18    gone - no logout
dmtsai2  work:0 work       Mon Oct 24 14:18 - 14:18  (00:00)
dmtsai2  pts/2             Mon Oct 24 14:18 - 14:18  (00:00)
dmtsai2  work:0 work       Mon Oct 24 14:18 - 14:18  (00:00)
```
舊的登入資訊會存到wtmp.1那邊

致於no-ip不能連的原因是似乎no-ip那邊出點狀況。[詳細原因](http://kfei.logdown.com/posts/207916-poor-no-ip)
 >  NOTICE: Our domains are still experiencing outages due to the Microsoft takedown. Please read our formal statement on the matter.

看來動物機沒被hack :)
白擔心了一下

今日發現`lastlog`實用的指令，查詢所有帳號最後一次登入的時間
可惜這個指令目前用不太到