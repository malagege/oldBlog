---
title: inotify-tools 監聽資料夾裡檔案異動工具
date: 2020-01-12 14:47:03
tags: [linux]
categories: 實用工具
---

最近做到大檔案處理方式
由於資料太多，程式在掃整個檔案目錄就花費很多時間
在想有什麼工具可以監聽資料夾這個動作
後來有找到 inotify-tools 可以做到
這邊就小記

<!--more-->

## 安裝

```
 sudo apt install inotify-tools 
```

## Linux 指令

監聽 /home 資料夾目錄(文件的訪問，寫入，修改，刪除)

### inotifywait

```
inotifywait -rm /home
```
note:
-r 遞迴目錄都會接收到監聽
-m 收到監聽異動會持續監聽，不會停止

ls 資料夾也會收到訊息

### inotifywatch 

```
inotifywatch -v -e access -e modify -t 60 -r /home
```

> Establishing watches...
> Setting up watch(es) on /home
> OK, /home is now being watched.
> Total of 5 watches.
> Finished establishing watches, now collecting statistics.
> Will listen for events for 30 seconds.
> total  access  modify  filename
> 13     3       10      /home/vagrant/


監聽access（文件读取)、modify(文件更改)檔案做統計個數

NOTE:
-v 顯示訊息
-e 要監聽的事件
-t 監聽幾秒鐘
-r 遞迴目錄都會接收到監聽

### 相關指令設定
> inotifywait
> 語法：
> inotifywait [-hcmrq] [-e ] [-t ] [–format ] [–timefmt ] [ … ]
> 參數：
> -h,–help
> 輸出幫助信息
> @
> 排除不需要監視的文件，可以是相對路徑，也可以是絕對路徑。
> –fromfile
> 從文件讀取需要監視的文件或排除的文件，一個文件一行，排除的文件以@開頭。
> -m, –monitor
> 接收到一個事情而不退出，無限期地執行。默認的行為是接收到一個事情後立即退出。
> -d, –daemon
> 跟–monitor一樣，除了是在後台運行，需要指定–outfile把事情輸出到一個文件。也意味著使用了–syslog。
> -o, –outfile
> 輸出事情到一個文件而不是標準輸出。
> -s, –syslog
> 輸出錯誤信息到系統日誌
> -r, –recursive
> 監視一個目錄下的所有子目錄。
> -q, –quiet
> 指定一次，不會輸出詳細信息，指定二次，除了致命錯誤，不會輸出任何信息。
> –exclude
> 正則匹配需要排除的文件，大小寫敏感。
> –excludei
> 正則匹配需要排除的文件，忽略大小寫。
> -t , –timeout
> 設置超時時間，如果為0，則無限期地執行下去。
> -e , –event
> 指定監視的事件。
> -c, –csv
> 輸出csv格式。
> –timefmt
> 指定時間格式，用於–format選項中的%T格式。
> –format
> 指定輸出格式。
> %w 表示發生事件的目錄
> %f 表示發生事件的文件
> %e 表示發生的事件
> %Xe 事件以「X」分隔
> %T 使用由–timefmt定義的時間格式
> inotifywatch
> 
> 語法：
> inotifywatch [-hvzrqf] [-e ] [-t ] [-a ] [-d ] [ … ]
> 參數：
> -h, –help
> 輸出幫助信息
> -v, –verbose
> 輸出詳細信息
> @
> 排除不需要監視的文件，可以是相對路徑，也可以是絕對路徑。
> –fromfile
> 從文件讀取需要監視的文件或排除的文件，一個文件一行，排除的文件以@開頭。
> -z, –zero
> 輸出表格的行和列，即使元素為空
> –exclude
> 正則匹配需要排除的文件，大小寫敏感。
> –excludei
> 正則匹配需要排除的文件，忽略大小寫。
> -r, –recursive
> 監視一個目錄下的所有子目錄。
> -t , –timeout
> 設置超時時間
> -e , –event
> 只監聽指定的事件。
> -a , –ascending
> 以指定事件升序排列。
> -d , –descending
> 以指定事件降序排列。

|||
|--- |--- |
|access|文件讀取|
|modify|文件更改。|
|attrib|文件屬性更改，如權限，時間戳等。|
|close_write|以可寫模式打開的文件被關閉，不代表此文件一定已經寫入數據。|
|close_nowrite|以只讀模式打開的文件被關閉。|
|close|文件被關閉，不管它是如何打開的。|
|open|文件打開。|
|moved_to|一個文件或目錄移動到監聽的目錄，即使是在同一目錄內移動，此事件也觸發。|
|moved_from|一個文件或目錄移出監聽的目錄，即使是在同一目錄內移動，此事件也觸發。|
|move|包括moved_to和 moved_from|
|move_self|文件或目錄被移除，之後不再監聽此文件或目錄。|
|create|文件或目錄創建|
|delete|文件或目錄刪除|
|delete_self|文件或目錄移除，之後不再監聽此文件或目錄|
|unmount|文件系統取消掛載，之後不再監聽此文件系統。|


資料來源: [inotify-tools使用方法介紹-Linux運維日誌](https://www.centos.bz/2012/06/inotify-tools-introduction/)

## 相關 inotify 設定

> 3、參數說明 
> max_user_instances：每個用戶創建inotify實例最大值
> max_queued_events：inotify隊列最大長度，如果值太小，會出現錯誤，導致監控文件不準確
> max_user_watches：要知道同步的文件包含的目錄數，可以用：
> [root@localhost]# find /home/rain -type d|wc -l 統計，必須保證參數值大於統計結果（/home/tan/uploadFile/為同步文件目錄）。

查看設定

```bash
sysctl -a | grep max_queued_events  
#fs.inotify.max_queued_events = 16384  
sysctl -a | grep max_user_watches  
#fs.inotify.max_user_watches = 8192  
#fs.epoll.max_user_watches = 798863  
sysctl -a | grep max_user_instances  
#fs.inotify.max_user_instances = 128
```

> 五、修改inotify參數
> 
> 1、命令修改
> [root@localhost bin]# sysctl -w fs.inotify.max_user_instances=130  
> fs.inotify.max_user_instances = 130
> 
> 2、文件修改
> [root@localhost]# vi /etc/sysctl.conf  
> #添加如下代碼  
> fs.inotify.max_user_instances=130

資料來源: [CentOS 6.9上inotify-tools 安裝及使用方法 - 1felse筆記](http://www.1felse.com/blog/view.do?blogId=ff8080816102bdbb01610d45232d005a)



## 相關串接方法


* [rsync + inotify-tools 實現 Linux 文件雙向實時同步 – Jacky's Blog](https://jackyu.cn/tech/rsync-plus-inotify-tools/)
監聽實作 rsync 方法

* [PHP用inotify擴展監控文件_chunyuan314的博客-CSDN博客](https://blog.csdn.net/chunyuan314/article/details/81750211)
發現 PHP 有擴充外掛可以使用

* [inotify-tools + php腳本實現Linux服務器文件監控並郵件提醒 - 黃小濤 - 博客園](https://www.cnblogs.com/spareribs/p/7722453.html)
跟上面 PHP 不太一樣
這邊他是用 inotify 寫入 log 方式去做比較是否異動

* [Rsync+Inotify 搭建實時同步數據 ‧ Jusene's Blog](https://jusene.github.io/2017/02/18/rsync-inotify/)
* [數據同步工具-RSYNC結合INOTIFY-TOOLS實現數據實時同步 | DevOpsSec](https://www.devopssec.cn/2018/08/23/%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%B7%A5%E5%85%B7-RSYNC%E7%BB%93%E5%90%88INOTIFY-TOOLS%E5%AE%9E%E7%8E%B0%E6%95%B0%E6%8D%AE%E5%AE%9E%E6%97%B6%E5%90%8C%E6%AD%A5/)
覺得這兩篇也寫得不錯

## 小小實驗

[cp 複製檔案可能沒注意的事情 | 程式狂想筆記](https://malagege.github.io/blog/2020/01/10/cp-%E8%A4%87%E8%A3%BD%E6%AA%94%E6%A1%88%E5%8F%AF%E8%83%BD%E6%B2%92%E6%B3%A8%E6%84%8F%E7%9A%84%E4%BA%8B%E6%83%85/)
實驗一下複製大檔案當下狀況
是忽看到 CLOSE_WRITE 做複製動作會比較安全

看了一下
-e close_write,delete,create,attrib
-e close_write,delete,moved_to
我看其他篇文章有下這個
這樣真的用 close_write 比較安全

```
/home/vagrant/vv/ CREATE file.txt
/home/vagrant/vv/ OPEN file.txt
/home/vagrant/vv/ MODIFY file.txt
/home/vagrant/vv/ MODIFY file.txt
/home/vagrant/vv/ MODIFY file.txt
/home/vagrant/vv/ MODIFY file.txt
/home/vagrant/vv/ MODIFY file.txt
...
/home/vagrant/vv/ MODIFY file.txt
/home/vagrant/vv/ MODIFY file.txt
/home/vagrant/vv/ MODIFY file.txt
/home/vagrant/vv/ MODIFY file.txt
/home/vagrant/vv/ MODIFY file.txt
/home/vagrant/vv/ MODIFY file.txt
/home/vagrant/vv/ MODIFY file.txt
/home/vagrant/vv/ CLOSE_WRITE,CLOSE file.txt
```

## 總結

我本來想找找網路上有沒有搭配 inotify-tools 做 RabbitMQ 動作
是有找到 [sof-object-listing/inotify at master · prashanthpai/sof-object-listing](https://github.com/prashanthpai/sof-object-listing/tree/master/inotify)，但未確認是不是能真的用
大多是做 **Web 監聽檔案** 和 **資料同步** 居多
但是 inotify-tools 看起來也有接收大量打案也有極限
不知道是不是一個好的解決方式
也要看其況

## 相關連結

* [inotify-tools使用方法介紹-Linux運維日誌](https://www.centos.bz/2012/06/inotify-tools-introduction/) {% asset_link web1.png 備份圖 %}
* [CentOS 6.9上inotify-tools 安裝及使用方法 - 1felse筆記](http://www.1felse.com/blog/view.do?blogId=ff8080816102bdbb01610d45232d005a) {% asset_link web2.png 備份圖 %}
* [rsync + inotify-tools 實現 Linux 文件雙向實時同步 – Jacky's Blog](https://jackyu.cn/tech/rsync-plus-inotify-tools/) {% asset_link web3.png 備份圖 %}
* [安全组应用案例_安全组_安全_云服务器 ECS-阿里云](https://help.aliyun.com/document_detail/25475.html?spm=5176.2020520101.121.1.211d5c9aAGX8X2#allowHttp) {% asset_link web4.png 備份圖 %}
* [PHP用inotify擴展監控文件_chunyuan314的博客-CSDN博客](https://blog.csdn.net/chunyuan314/article/details/81750211)
* [inotifywait命令_Linux inotifywait 命令用法詳解：異步文件系統監控機制](https://man.linuxde.net/inotifywait)
* [inotify-tools + php腳本實現Linux服務器文件監控並郵件提醒 - 黃小濤 - 博客園](https://www.cnblogs.com/spareribs/p/7722453.html) {% asset_link web5.png 備份圖 %}
* [Rsync+Inotify 搭建實時同步數據 ‧ Jusene's Blog](https://jusene.github.io/2017/02/18/rsync-inotify/) {% asset_link web6.png 備份圖 %}
* [數據同步工具-RSYNC結合INOTIFY-TOOLS實現數據實時同步 | DevOpsSec](https://www.devopssec.cn/2018/08/23/%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%B7%A5%E5%85%B7-RSYNC%E7%BB%93%E5%90%88INOTIFY-TOOLS%E5%AE%9E%E7%8E%B0%E6%95%B0%E6%8D%AE%E5%AE%9E%E6%97%B6%E5%90%8C%E6%AD%A5/) {% asset_link web7.png 備份圖 %}









**彩蛋**
突然想到上次研究 Oauth API
發現有些token 能做到時效性
但一直沒有時間紀錄
看到有一篇說 token 時效性問題
突然想到這樣不就會買台時間不一樣的問題
但後來發現原來 oauth 傳的時間是 duration 時間
所以不會有這個問題
* [開發者必備知識 - HTTP認證（HTTP Authentication） - Carson's Tech Note](https://carsonwah.github.io/http-authentication.html)
* [thephpleague/oauth2-server: A spec compliant, secure by default PHP OAuth 2.0 Server](https://github.com/thephpleague/oauth2-server)
* [揭秘Token的工作原理 | Crazy's Blog](https://yhv5.com/token_1532.html?)
* [微服務架構 #4, 如何強化微服務的安全性? API Token / JWT 的應用 — 安德魯的部落格](https://columns.chicken-house.net/2016/12/01/microservice7-apitoken/) {% asset_link web8.png 備份圖 %}
* [Token 认证的来龙去脉 - 掘金](https://juejin.im/post/5a6c60166fb9a01caf37a5e5)
* [iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/m/articles/10015681)
* [RSA數位簽章的簡單流程 | Litfal的S/L中心 - 點部落](https://dotblogs.com.tw/litfal/2014/03/08/144298)
* [[PHP] 範例實做 RSA, 公鑰、私鑰非對稱加密、解密演算法 - 串接 iOS,Android - 工作玩樂實驗室](https://cola.workxplay.net/encrypt-and-decrypt-data-in-php-using-rsa-example/)
* [OAuth2 認證機制 Token 原理 - 科技新人](https://www.vnewin.com/day18-laravel-passport-oauth2-token/)
* [非对称加密RSA详解 | Wuman's Blog](https://wumansgy.github.io/2018/11/03/%E9%9D%9E%E5%AF%B9%E7%A7%B0%E5%8A%A0%E5%AF%86RSA%E8%AF%A6%E8%A7%A3/)