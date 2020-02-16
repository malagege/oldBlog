---
title: logrotate 自動切割 log 小記
date: 2020-01-27 19:49:59
tags: [linux, log]
categories: Linux
---

最近 Pi 2號機有時候 ssh 不穩
重開數據機或重插網路線就恢復正常
不知道是哪邊問題
想寫 ping log 來排除一些狀況

<!--more-->

## ping shell

首先，我需要準備 ping 的 shell 程式

```bash
#!/bin/bash

IP=$1

date "+%Y-%m-%d %H:%M:%S"
echo "PING HOST ${IP}"

while [ TRUE ]
do
        ping -c 10 ${IP}
        sleep 300 #300秒檢查
done
```

開啟 tmux 分割多個視窗
`./test.sh > ping_tw.yahoo.com.log`

更多 ping 相關指令
[Ping 指令用法教學：檢查網路連線狀態與品質 - G. T. Wang](https://blog.gtwang.org/linux/windows-linux-ping-command-tutorial/) {% asset_link ping.png 備份圖 %}

### 沒辦法中斷(Ctrl + C) 程式

[你无法用ctrl c 杀死的进程 - 简书](https://www.jianshu.com/p/a028c9eef2c2)

簡單解決方法，用 htop kill掉程式吧...

### shell 比較要注意加"

> 有兩種解決方法：
> if [ "$app-running" == "" ];
> 或是
> if [[ $app-running == "" ]];

[Linux 的 shell script 中，遇到 unary operator expected 的解決方法](https://medium.com/micheh/linux-%E7%9A%84-shell-script-%E4%B8%AD-%E9%81%87%E5%88%B0-unary-operator-expected-%E7%9A%84%E8%A7%A3%E6%B1%BA%E6%96%B9%E6%B3%95-3b4725d3fdf1)

### shell error print

[Writing shell scripts - Lesson 14: Errors and Signals and Traps (Oh My!) - Part 1](http://linuxcommand.org/lc3_wss0140.php)


## logrotate


### 更換 log 方法

其實 logrotate 切割 log 我有在想不會跟線上執行程式衝到嗎?
[Linux 日志切割神器 logrotate 原理介绍和配置详解 | HelloDog](https://wsgzao.github.io/post/logrotate/) {% asset_link web1.png 備份圖 %}
這邊裡面有詳細說明

> 如上也就是 logrotate 的默認操作方式，也就是 mv+create 執行完之後，通知應用重新在新文件寫入即可。mv+create 成本都比較低，幾乎是原子操作，如果應用支持重新打開日誌文件，如 syslog, nginx, mysql 等，那麼這是最好的方式。
> 不過，有些程序並不支持這種方式，壓根沒有提供重新打開日誌的接口；而如果重啟應用程序，必然會降低可用性，為此引入了如下方式。

這邊讓我想到之前有做，mv 跟 cp 實驗([cp 複製檔案可能沒注意的事情 | 程式狂想筆記](https://malagege.github.io/blog/2020/01/10/cp-%E8%A4%87%E8%A3%BD%E6%AA%94%E6%A1%88%E5%8F%AF%E8%83%BD%E6%B2%92%E6%B3%A8%E6%84%8F%E7%9A%84%E4%BA%8B%E6%83%85/))

我計畫使用 `./test.sh > ping_tw.yahoo.com.log`
但這個好像不可行 我是使用指令倒出 log

### 輸出問題

我想預防上述的問題
我想到下面方法
**不過先說明以下 shell 不能正常執行**
**不過先說明以下 shell 不能正常執行**
**不過先說明以下 shell 不能正常執行**
```bash
#!/bin/bash

IP=$1
if [ "${IP}" = "" ];then
    echo "must need put IP" 1>&2
    exit 1
fi

OUTLOG=" >> ping_${IP}.log"


while [ TRUE ]
do
        echo "=========================================" $OUTLOG
        echo "=========================================" $OUTLOG
        date "+%Y-%m-%d %H:%M:%S" $OUTLOG
        echo "PING HOST: ${IP}" $OUTLOG

        ping -c 10 ${IP}  $OUTLOG
        sleep 300 #300秒檢查
done

```


```shell
log() {
  if [ -n "$logfile" ]; then
    printf '%s\n' "$@" >> "$logfile"
  else
    printf '%s\n' "$@"
  fi
}

log "$buf"
```

可修正成這個方法[shell script - Elegant solution to echo to either stdout or file in bash - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/336224/elegant-solution-to-echo-to-either-stdout-or-file-in-bash)

因為~~我比較懶~~行數比較少，所以我就寫在每一行 XD
但其實可以不用這樣寫
可參考後續文章
```bash
#!/bin/bash

IP=$1
if [ "${IP}" = "" ];then
    echo "must need put IP" 1>&2
    exit 1
fi

OUTLOG=" >> ping_${IP}.log"

while [ TRUE ]
do
        echo "========================================="  >> ping_${IP}.log
        echo "========================================="  >> ping_${IP}.log
        date "+%Y-%m-%d %H:%M:%S"  >> ping_${IP}.log
        echo "PING HOST: ${IP}"  >> ping_${IP}.log

        ping -c 10 ${IP}   >> ping_${IP}.log
        sleep 300 #300秒檢查
done

```

這樣我應該就能使用 CREATE 機制
終於到我們重頭戲了


## logrotate 

###  相關教學網站

* [[CENTOS7] 使用 logrotate 來整理wildfly日誌檔 @新精讚](http://n.sfs.tw/content/index/12926)
* [Linux 日誌切割神器 logrotate 原理介紹和配置詳解 | HelloDog](https://wsgzao.github.io/post/logrotate/)
* [使用 logrotate 定期整理 Rails Log 檔案 – ihower { blogging }](https://ihower.tw/blog/archives/3565)
* [linux 日志定时轮询流程详解 - 簡書](https://www.jianshu.com/p/ea7c2363639c)
* [linux - How to insert a date inside the filename with logrotate - Stack Overflow](https://stackoverflow.com/questions/9555769/how-to-insert-a-date-inside-the-filename-with-logrotate)
* [linux 日志管理服务 logrotate | hatlonely的小站](http://www.hatlonely.com/2018/04/16/linux-%E6%97%A5%E5%BF%97%E7%AE%A1%E7%90%86%E6%9C%8D%E5%8A%A1-logrotate/index.html)
* [linux - logrotate configuration file syntax - multiple wildcard entries possible? - Super User](https://superuser.com/questions/255951/logrotate-configuration-file-syntax-multiple-wildcard-entries-possible)

### logrotate 重點小記

相關自訂 logrotate 設定可放在 `/etc/logrotate.d`
原理可看[Linux 日誌切割神器 logrotate 原理介紹和配置詳解 | HelloDog](https://wsgzao.github.io/post/logrotate/) 運行機制

其實最原始的 conf設定在 `/etc/logrotate.conf`
看到`include /etc/logrotate.d`就能明白為什麼會自動載入

其他看到的重點
>    配置文件裡一定要配置 rotate 文件數目 這個參數。如果不配置默認是 0 個，也就是只允許存在一份日誌，剛切分出來的日誌會馬上被刪除。多麼痛的領悟，說多了都是淚。
>    執行 logrotate 命令最好加 -f 參數，不然有時候配置文件修改的內容不生效。



## 我的 ping 程式設定

/home/pi/ping/ping_*.log {
  daily
  dateext
  missingok
  rotate 30
  notifempty
  create 664 pi pi
}



## 訊號問題

看到有些需要做 HUP 動作，讓程式重新讀 log 方法
使用**create**需要注意通知程式做，copytruncate 不需要做

> create
> 
> create 是默認的方案，可以通過 create 命令配置文件的權限和屬組設置。
> 
> 這個方案的思路是：** 重命名原日誌文件，創建新的日誌文件 **。
> 
> 詳細步驟如下：
> 
>     1. 重命名正在輸出的日誌文件。 因為重命名只修改文件（目錄亦文件）的名稱，而進程操作文件使用的是 inode，所以並不影響原程序繼續輸出日誌。
> 
>     2. 創建新的日誌文件。 文件名和原日誌文件一樣，注意，此時只是文件名稱一樣，而 inode 編號不同，程序輸出的日誌還是往原日誌文件輸出。
> 
>     3. 最後通過某些方式通知程序，重新打開日誌文件。 由於重新打開日誌文件會用到文件路徑而非 inode 編號，所以打開的是新的日誌文件。
> 
> 簡而言之，logrotate 的默認操作方式，就是 mv+create 執行完之後，通知應用重新在新文件寫入即可。mv+create 成本都比較低，幾乎是原子操作，如果應用支> 持重新打開日誌文件，如 syslog、 nginx、mysql 等，那麼這是最好的方式。
> 
> 不過，有些程序並不支持這種方式，壓根兒沒有提供重新打開日誌的接口，而如果重啟應用程序，必然會降低可用性，為此引入了 copytruncate 方式。
> copytruncate
> 
> copytruncate 是把正在輸出的日誌拷（copy）一份出來，再清空 （trucate）原來的日誌。
> 
> 詳細步驟如下：
> 
>     1. 將當前正在輸出的日誌文件複製為目標文件，此時程序仍然將日誌輸出到原來文件中，原文件名也沒有變。
>     2. 清空原日誌文件，程序仍然還是輸出到原日誌文件中，因為清空文件只把文件的內容刪除了，而 inode 並沒改變。
> 
> 所以 copytruncate 也就是先複製一份文件，然後清空原有文件。
> 
> 通常來說，清空操作比較快，但是如果日誌文件太大，那麼複製就會比較耗時，從而可能導致部分日誌丟失。不過這種方式不需要應用程序的支持即可使用。

參考：[Linux 日志切割工具 Logrotate | PwD](https://www.pwdpwd.com/post/2020-01-03-linux-logrotate/)

[linux 日志定时轮询流程详解(logrotate) - 生活费 - 博客园](https://www.cnblogs.com/276815076/p/7053640.html)
[kill -HUP 命令的使用-赤练挚爱-51CTO博客](https://blog.51cto.com/pengjc/1774210)


### 意外小實驗結果

雖然不是很清楚他的原理，但我剛剛做一個實驗
得出很有趣的結果

> 如果是用 > 重定向輸出的日誌，如 test_app > stdout.log, copytruncate 則無法裁剪原始日誌文件 stdout.log, 它將持續增漲下去。而用 >> 就沒問題了，test-app >> stdout.log，日誌滾動後 stdout.log 大小將變為零。

[Linux 下配置滚动日志之 logrotate | 隔叶黄莺 Yanbin Blog - 软件编程实践](https://yanbin.blog/linux-config-log-ratation-logrotate/)

這邊把  ping.sh 標準輸出註解掉了

```bash
#!/bin/bash

IP=$1
if [ "${IP}" = "" ];then
    echo "must need put IP" 1>&2
    exit 1
fi

OUTLOG=" >> ping_${IP}.log"

while [ TRUE ]
do
        echo "========================================="  #>> ping_${IP}.log
        echo "========================================="  #>> ping_${IP}.log
        date "+%Y-%m-%d %H:%M:%S"  #>> ping_${IP}.log
        echo "PING HOST: ${IP}"  #>> ping_${IP}.log


        ping -c 10 ${IP}   #>> ping_${IP}.log
        #sleep 300 #300秒檢查
done
```

### 使用 create 做 ./ping.sh 127.0.0.1 >> ping_127.0.0.1.log 

做 logrotate(`sudo logrotate -vf /etc/logrotate.d/ping`) 後會產生ping_127.0.0.1.log,ping_127.0.0.1.log-2020-01-27
程式持續跑的log會加在ping_127.0.0.1.log-2020-01-27
如果還記得流程的話，上面有說要 HUP 通知信號跟程式講重抓 log 寫入設定檔

```
 3. 最後通過某些方式通知程序，重新打開日誌文件。 由於重新打開日誌文件會用到文件路徑而非 inode 編號，所以打開的是新的日誌文件。
```

{% asset_img 1.png 實驗成果 %}

### 使用 copytruncate 做 ./ping.sh 127.0.0.1 >> ping_127.0.0.1.log 

做 logrotate(`sudo logrotate -vf /etc/logrotate.d/ping`) 後會產生ping_127.0.0.1.log,ping_127.0.0.1.log-2020-01-27
程式持續跑的log會加在ping_127.0.0.1.log
但聽說這種作法有機率遺失 log

{% asset_img 2.png 實驗成果 %}

## >

這個應該沒什麼機會用到
但對 create , copytruncate 都會造成 log  紀錄有問題
結果我就不貼了

## 測試使用

建議加 sudo root執行

1. sudo logrotate /etc/logrotate.d/ping
執行 logrotate

2. sudo logrotate -d /etc/logrotate.d/ping
測試日誌切割 (如果文件的時間小於一天，不會執行切割日誌 )

3. sudo logrotate -vf /etc/logrotate.d/ping
強制輪詢切割日誌 ( 為了便於我們直觀的觀察測試結果,建議大家手動試一下 )
-f (--force), 就是把不管日誌文件大小是否符合要求，強制執行日誌滾動操作

參照:[linux环境下使用logrotate工具实现nginx日志切割 - 知乎](https://zhuanlan.zhihu.com/p/24880144)


### 測試完建議刪除檔案

測試完建議刪除檔案，否則會出現如下錯誤
error: destination /home/pi/ping/ping_127.0.0.1.log-20200127 already exists, skipping rotation

[linux - logrotate suffix: dateext + rotate - Stack Overflow](https://stackoverflow.com/questions/25845752/logrotate-suffix-dateext-rotate)

最後 shell 用 tmux 爽爽執行 ping.sh 程式

### logrotate 

配置選項

常用選項：

    daily - 指定轉儲週期為每天
    weekly - 指定轉儲週期為每週
    monthly - 指定轉儲週期為每月
    rotate count - 指定日誌文件刪除之前轉儲的次數，0 指沒有備份，5 指保留 5 個備份
    tabooext [+] list - 讓 logrotate 不轉儲指定擴展名的文件，缺省的擴展名是：.rpm-orig、.rpmsave、v 和 ～
    missingok - 在日誌輪循期間，任何錯誤將被忽略，例如 * 文件無法找到 * 之類的錯誤
    size size - 當日誌文件到達指定的大小時才轉儲，bytes (缺省) 及 KB (sizek) 或 MB (sizem)
    compress - 通過 gzip 壓縮轉儲以後的日誌
    nocompress - 不壓縮
    copytruncate - 用於還在打開中的日誌文件，把當前日誌備份並截斷
    nocopytruncate - 備份日誌文件但是不截斷
    create mode owner group - 轉儲文件，使用指定的文件模式創建新的日誌文件
    nocreate - 不建立新的日誌文件
    delaycompress - 和 compress 一起使用時，轉儲的日誌文件到下一次轉儲時才壓縮
    nodelaycompress - 覆蓋 delaycompress 選項，轉儲同時壓縮。
    errors address - 轉儲時的錯誤信息發送到指定的 Email 地址
    ifempty - 即使是空文件也轉儲，這是 logrotate 的缺省選項。
    notifempty - 如果是空文件的話，不轉儲
    mail address - 把轉儲的日誌文件發送到指定的 E-mail 地址
    nomail - 轉儲時不發送日誌文件
    olddir directory - 儲後的日誌文件放入指定的目錄，必須和當前日誌文件在同一個文件系統
    noolddir - 轉儲後的日誌文件和當前日誌文件放在同一個目錄下
    prerotate/endscript - 在轉儲以前需要執行的命令可以放入這個選項對之間，這兩個關鍵字必須單獨成行
    sharedscripts - 默認情況下，prerotate/endscript 內定義的命令會對匹配到的所有文件 (比如： /var/log/news/*）都執行一次，如果指定為 sharedscripts，則這些命令只會執行一次
    dateext - 對於轉儲的日誌文件加上日期後綴

參考:[Linux 日誌切割工具 Logrotate | PwD](https://www.pwdpwd.com/post/2020-01-03-linux-logrotate/) {% asset_link web2.png 備份圖 %}


## 總結

其實這篇不重要刪減，所以看的內容非常雜
不過經過這次實驗有清楚了解到要怎麼使用 logrotate

後續如果程式要做 HUP 訊號要怎麼處理?之後有空在研究


