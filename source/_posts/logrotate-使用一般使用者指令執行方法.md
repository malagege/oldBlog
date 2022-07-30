---
title: logrotate 使用一般使用者指令執行方法
date: 2022-07-31 00:19:11
tags: [linux,log,logrotate]
categories: Linux
---

一般我們開發人員沒有權限，但有一般使用者可以使用 logrotate ，就不用自幹 Shell Script 指令，還能做多樣化設定。

先前文章有研究到[logrotate 自動切割 log 小記 | 程式狂想筆記](https://malagege.github.io/blog/2020/01/27/logrotate-%E8%87%AA%E5%8B%95%E5%88%87%E5%89%B2-log-%E5%B0%8F%E8%A8%98/)，但都沒實戰。

<!--more-->

## 使用發法(請看第一、三個指令)

```bash=
logrotate -s /home/project/logrotate/status -v    -d /home/project/logrotate/pbci.conf
# ~~不會執行~~，有看到文章說檔案小於一天不會執行

logrotate -s /home/project/logrotate/status -vf    -d /home/project/logrotate/pbci.conf
# 不會執行因為debug mode關係

logrotate -s /home/project/logrotate/status -vf   /home/project/logrotate/pbci.conf
# 會執行
```

`-s`  可指定一般使用者執行路徑，會存`status`檔案。

https://github.com/logrotate/logrotate/issues/80

`-d` debug 模式是呼不會執行

## 網路上完整教學文章

推薦看[Linux日志切割神器logrotate原理介绍和配置详解 - 知乎](https://zhuanlan.zhihu.com/p/90507023)  !!!備份圖!!!
摘要幾個重點。

```
logrotate [OPTION...] <configfile>
-d, --debug ：debug 模式，測試配置文件是否有錯誤。
-f, --force ：強制轉儲文件。
-m, --mail=command ：壓縮日誌後，發送日誌到指定郵箱。
-s, --state=statefile ：使用指定的狀態文件。
-v, --verbose ：顯示轉儲過程。
```

```
[root@localhost ~]# logrotate -d /etc/logrotate.d/log_file

reading config file /etc/logrotate.d/log_file
reading config info for /var/log/log_file 

Handling 1 logs

rotating pattern: /var/log/log_file  monthly (5 rotations)
empty log files are not rotated, old logs are removed
considering log /var/log/log_file
  log does not need rotating
not running postrotate script, since no logs were rotated
```
> 正如我們從上面的輸出結果可以看到的，logrotate 判斷該輪循是不必要的。如果文件的時間小於一天，這就會發生了。

```
 常見配置參數

daily ：指定轉儲週期為每天weekly ：指定轉儲週期為每週monthly ：指定轉儲週期為每月rotate count ：指定日誌文件刪除之前轉儲的次數，0 指沒有備份，5 指保留 5 個備份tabooext [+] 
list：讓 logrotate 不轉儲指定擴展名的文件，
缺省的擴展名是：.rpm-orig, .rpmsave, v, 和～
missingok：在日誌輪循期間，任何錯誤將被忽略，例如 「文件無法找到」 之類的錯誤。size 
size：當日誌文件到達指定的大小時才轉儲，bytes (缺省) 及 KB (sizek) 或 MB (sizem)
compress： 通過 gzip 壓縮轉儲以後的日誌
nocompress： 不壓縮copytruncate：用於還在打開中的日誌文件，
把當前日誌備份並截斷nocopytruncate： 備份日誌文件但是不截斷create mode owner 
group ： 轉儲文件，使用指定的文件模式創建新的日誌文件
nocreate： 不建立新的日誌文件
delaycompress： 和 compress 一起使用時，轉儲的日誌文件到下一次轉儲時才壓縮
nodelaycompress： 覆蓋 delaycompress 選項，轉儲同時壓縮。
errors address ： 專儲時的錯誤信息發送到指定的 Email 地址
ifempty ：即使是空文件也轉儲，這個是 logrotate 的缺省選項。
notifempty ：如果是空文件的話，不轉儲mail 
address ： 把轉儲的日誌文件發送到指定的 E-mail 地址
nomail ： 轉儲時不發送日誌文件olddir 
directory：儲後的日誌文件放入指定的目錄，必須和當前日誌文件在同一個文件系統
noolddir： 轉儲後的日誌文件和當前日誌文件放在同一個目錄下prerotate/
endscript： 在轉儲以前需要執行的命令可以放入這個對，這兩個關鍵字必須單獨成行
```


## 設定(範例)

### 每天備分(copytruncate)，保留30天

```
/Project/logs/batch/*.log {
    daily
    dateext
    missingok
    rotate 30
    notifempty
    copytruncate
}


/Project/logs/oxProject/*.log {
    daily
    dateext
    missingok
    rotate 30
    notifempty
    copytruncate
}
```

## 備份後做壓縮檔

  compress  ->做壓縮
  delaycompress -> 延遲最新檔案不做壓縮

參考[使用 logrotate 定期整理 Rails Log 檔案 – ihower { blogging }](https://ihower.tw/blog/archives/3565)
```
# Rotate Rails application logs
/path/to/your/rails/current/log/*.log {
  daily
  dateext
  missingok
  rotate 65535
  compress
  delaycompress
  notifempty
  copytruncate
}

```

## 檔案日誌超過幾MB做輪替

[[Linux] logrotate 設定 | 阿輝的零碎筆記 - 點部落](https://dotblogs.com.tw/grayyin/2019/05/14/113632)

```
    daily                   #每天切割
    size=100M               #每100M做一次rotation，如果 weekly 與 size 這兩個設定都有加，表示如果檔案不到100M便不會生成rotation
``` 


## 線上產生工具

看過設定一輪，在看上面教學大概就會使用了。

[Log Rotate Config Generator](http://scoin.github.io/logrotate-tool/)




[登录文件的轮替 logrotate | Linux 系统教程（笔记）](https://zq99299.github.io/linux-tutorial/tutorial-basis/18/03.html#logrotate-%E7%9A%84%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6)