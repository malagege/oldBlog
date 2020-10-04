---
title: Linux IO 效能研究怎麼看
date: 2020-10-03 01:16:29
tags: [linux, io]
categories: Linux
---

最近除了看 CPU 使用率過高
之前 Raspberry Pi 使用 NextCloud 的時候速度非常慢
一直以為是 CPU 關係，但後來發現不是
官網友提到是 IO 效能關係

<!--more-->

一直以為 IO 效能跟硬碟有關係
但後來研究一翻，參考網路上一些文章
後來 CPU, RAM, Network ... 等等都有關係

## IO 效能怎麼看?

[Linux 性能监控 —— 磁盘 I/O_wenniuwuren-CSDN博客](https://blog.csdn.net/wenniuwuren/article/details/50558183)

{% asset_img 1.png top %}

> 二. 如何衡量磁盤 I/O 存在瓶頸
> 磁盤 I/O 是否存在瓶頸取決於當前機器 CPU 的總核數（總核數計算移步我的另一篇博文: Linux 性能監控 ---- Load Average）。 公式： X(IO) = 1/（cpu 總核數） 用來衡量磁盤 I/O 情況， 以上圖的機器為例子， 這台機器 2 CPU 12 核 也就是總核心數為 2*12 = 24， X(IO) = 0.04166666 = 4.17%。 
如果上圖指標長時間大於 4.17%， 那麼我們的網站響應可能就會變慢了。  

簡單來說就是 top
CPU 那一行 13 wa 那一行
 X(IO) = 1/（cpu 總核數）
我樹梅派沒有使用百分比，不過他這邊應該沒放


## IO 測試方法

[blog.toright.com/posts/5051/linux-disk-io-效能測試.html/amp](https://blog.toright.com/posts/5051/linux-disk-io-%E6%95%88%E8%83%BD%E6%B8%AC%E8%A9%A6.html/amp)

使用最簡單方法

```bash
dd if=/dev/zero of=/tmp/bench bs=1M count=1024 && rm /tmp/bench
```

在樹梅派使用 SD 做 wa有到 0.25 左右，不過改在 USB 上面
wa到 0.5 上下，CPU 雖然沒有吃很高
但是這時候很明顯感覺 pietty 操作跟重新開市窗操作連線會變慢
看來程式處理速度跟這個數字也有關


## 安裝 iostat

```bash
sudo apt install sysstat 
```
- [解決 iostat command not found](https://www.opencli.com/linux/fix-iostat-command-not-found)

- [linux wa%過高，iostat查看io狀況 - 測試天堂 - 51Testing軟件測試網 51Testing軟件測試網-軟件測試人的精神家園](http://www.51testing.com/html/48/202848-242043.html)

## wa 高不一定是出在硬碟 io


* [傲笑紅塵路: 監控Linux 系統效能狀況](https://www.lijyyh.com/2012/01/linux.html) {% asset_link web1.png 備份圖 %}

可能是網路等等其他
這邊有查到 sar 可以查詢 CPU, RAM, Disk I/O, Network 


網路查詢方法
[使用sar工具进行cpu/mem/io/network等性能分析_@_囚徒-2018_的家园-CSDN博客](https://blog.csdn.net/wangjianno2/article/details/39256163)
[12. sar 找出系统瓶颈的利器 — Linux Tools Quick Tutorial](https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/sar.html)
[记一次服务端 IO 瓶颈问题定位 · TesterHome](https://testerhome.com/topics/20606)


[使用 sar 記錄系統資源使用狀態 | TechNote](http://technote.aven-network.com/826/using-sar-log-system-resource-status)

[菜鳥工程師 肉豬: Linux htop 系統程序監控工具簡介](https://matthung0807.blogspot.com/2019/06/linux-htop.html?m=0)

[7 個實例教你使用 sar 命令生成CPU、內存和輸入輸出埠的報告 | Linux Story](https://linuxstory.org/generate-cpu-memory-io-report-sar-command/zh-tw/)
[如何在Raspberry Pi 用USB碟開機 – Wayne's Garden](https://wayne65.ap.ngrok.io/wp/%E5%A6%82%E4%BD%95%E5%9C%A8raspberry-pi-%E7%94%A8usb%E7%A2%9F%E9%96%8B%E6%A9%9F/)
[案例：系统的 CPU 使用率很高，但为啥却找不到高 CPU 的应用？ | Laravel China 社区](https://learnku.com/articles/39516)

## 20201004 查 Raspberry pi 的 top CPU 異常飆高

### PI 環境簡單介紹

之前有使用 rclone 掛載 Google 雲端硬碟
但是最近使用 ftp(vsftpd)下載完檔案 發現 wa CPU 標高
(中途有遇到我的D朝下載超過容量關係，其他下載失敗)
swap 也吃了 80MB(總共 100 MB)
swap 應該可能是因為我 rclone 掛載模式關係
wa 沒有降下來原因我覺得要找一下

## 用上面指令查詢經過(可跳過這篇)

這邊有使用 SAR 查看
```
pi@PI203:~ $ sar -r 2 5
Linux 4.19.118-v7+ (PI203)      2020年10月03日  _armv7l_        (4 CPU)

03時32分26秒 kbmemfree   kbavail kbmemused  %memused kbbuffers  kbcached  kbcommit   %commit  kbactive   kbinact   kbdirty
03時32分28秒     89612    642628    238560     25.16     14860    522124    777284     73.98    185960    542316         0
03時32分30秒     89612    642628    238560     25.16     14860    522124    777284     73.98    186020    542316         0
03時32分32秒     89604    642620    238568     25.16     14860    522124    777284     73.98    186020    542316         0
03時32分34秒     89620    642636    238544     25.16     14860    522128    779252     74.17    186252    542320         0
03時32分36秒     89604    642620    238564     25.16     14860    522124    779252     74.17    186108    542316         0
平均時間：     89610    642626    238559     25.16     14860    522125    778071     74.05    186072    542317         0
pi@PI203:~ $ sar -r 2 5
Linux 4.19.118-v7+ (PI203)      2020年10月03日  _armv7l_        (4 CPU)

03時32分41秒 kbmemfree   kbavail kbmemused  %memused kbbuffers  kbcached  kbcommit   %commit  kbactive   kbinact   kbdirty
03時32分43秒     89856    642872    238312     25.13     14860    522124    777832     74.03    185968    542316         0
03時32分45秒     89856    642872    238312     25.13     14860    522124    777832     74.03    185968    542316         0
03時32分47秒     89864    642880    238304     25.13     14860    522124    777832     74.03    185968    542316         0
03時32分49秒     89864    642880    238304     25.13     14860    522124    777832     74.03    185968    542316         0
03時32分51秒     89864    642880    238304     25.13     14860    522124    777832     74.03    185968    542316         0
平均時間：     89861    642877    238307     25.13     14860    522124    777832     74.03    185968    542316         0
```

這邊 commit 怪怪的，網路查好像 commit 需要大於 100%

> %commit
> 當前需要的內存/RAM+swap，要保證大於100%，因為內核通常都overcommit內存。

- [从vmstat命令里看服务器瓶颈 | Chris Chan's BLOG](https://rorschachchan.github.io/2018/01/03/%E4%BB%8Evmstat%E5%91%BD%E4%BB%A4%E9%87%8C%E7%9C%8B%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%93%B6%E9%A2%88/)
- [vmstat 的用法和判别系统瓶颈_RisenWang 的博客-CSDN博客](https://blog.csdn.net/risen16/article/details/53355417)

```
pi@PI203:~ $ vmstat 1 20
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  1  85568  89920  14888 559472    0    0     2     1    3    2  1  1 98  1  0
 0  1  85568  89896  14888 559472    0    0     0     0  178  266  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  175  272  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  164  250  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  202  304  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  183  283  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  180  267  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  161  244  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  166  253  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  177  275  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  190  295  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  188  289  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  179  270  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  177  275  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  175  266  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  164  254  0  0 75 25  0
 1  1  85568  89896  14888 559472    0    0     0     0  187  289  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  169  255  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  181  283  0  0 75 25  0
 0  1  85568  89896  14888 559472    0    0     0     0  171  248  0  0 75 25  0
```

這邊wa 這麼高，沒有降下來...這邊 top 應該跟 wa 是一樣的東西

## vsftp 程式 uninterruptible sleep 狀態


參照這個shell 找到 vsftp 有問題[iowait 过高问题的查找及解决linux | 张志明的个人博客](https://www.503error.com/2016/iowait-%E8%BF%87%E9%AB%98%E9%97%AE%E9%A2%98%E7%9A%84%E6%9F%A5%E6%89%BE%E5%8F%8A%E8%A7%A3%E5%86%B3linux/1035.html)
裡面有提到用 iotop 但我看數字都正常

### 查看 uninterruptible sleep 程式

```
 # for x in `seq 1 1 10`; do ps -eo state,pid,cmd | grep "^D"; echo "----"; sleep 5; done
D 18634 /usr/sbin/vsftpd /etc/vsftpd.conf
----
D 18634 /usr/sbin/vsftpd /etc/vsftpd.conf
----
```

這邊除了這個方法
```
watch -n 1 "(ps aux | awk '\$8 ~ /D/  { print \$0 }')"
```
[Linux - How can I see what's waiting for disk IO - Server Fault](https://serverfault.com/questions/61510/linux-how-can-i-see-whats-waiting-for-disk-io)

[linux - Find process causing IO wait time - Server Fault](https://serverfault.com/questions/616570/find-process-causing-io-wait-time)
iotop 不知道能不能看到



### lsof -p 18634

```
COMMAND   PID USER   FD      TYPE     DEVICE SIZE/OFF    NODE NAME
vsftpd  18634   pi  cwd   unknown                             /mnt/gdrive/ACG/TV/202007/沒落要塞 DECA-DENCE (stat: Permission denied)
vsftpd  18634   pi  rtd       DIR      179,2     4096       2 /
vsftpd  18634   pi  txt       REG      179,2   113240   24973 /usr/sbin/vsftpd
vsftpd  18634   pi  mem       REG      179,2    42632    1674 /lib/arm-linux-gnueabihf/libnss_files-2.28.so
vsftpd  18634   pi  mem       REG      179,2    26600    1697 /lib/arm-linux-gnueabihf/librt-2.28.so
vsftpd  18634   pi  mem       REG      179,2    17956    1637 /lib/arm-linux-gnueabihf/libcap-ng.so.0.0.0
vsftpd  18634   pi  mem       REG      179,2   130416    1693 /lib/arm-linux-gnueabihf/libpthread-2.28.so
vsftpd  18634   pi  mem       REG      179,2     9768    1644 /lib/arm-linux-gnueabihf/libdl-2.28.so
vsftpd  18634   pi  mem       REG      179,2   116032    1580 /lib/arm-linux-gnueabihf/libaudit.so.1.0.0
vsftpd  18634   pi  mem       REG      179,2    71576    1671 /lib/arm-linux-gnueabihf/libnsl-2.28.so
vsftpd  18634   pi  mem       REG      179,2  1296004    1636 /lib/arm-linux-gnueabihf/libc-2.28.so
vsftpd  18634   pi  mem       REG      179,2    17872    1638 /lib/arm-linux-gnueabihf/libcap.so.2.25
vsftpd  18634   pi  mem       REG      179,2  2118048   15238 /usr/lib/arm-linux-gnueabihf/libcrypto.so.1.1
vsftpd  18634   pi  mem       REG      179,2   454924   15239 /usr/lib/arm-linux-gnueabihf/libssl.so.1.1
vsftpd  18634   pi  mem       REG      179,2    50896    2417 /lib/arm-linux-gnueabihf/libpam.so.0.84.2
vsftpd  18634   pi  mem       REG      179,2    30508    1715 /lib/arm-linux-gnueabihf/libwrap.so.0.7.6
vsftpd  18634   pi  mem       REG      179,2    17708   14650 /usr/lib/arm-linux-gnueabihf/libarmmem-v7l.so
vsftpd  18634   pi  mem       REG      179,2   138604    1559 /lib/arm-linux-gnueabihf/ld-2.28.so
vsftpd  18634   pi    0u     sock        0,8      0t0 2903508 protocol: TCPv6
vsftpd  18634   pi    1u     sock        0,8      0t0 2903508 protocol: TCPv6
vsftpd  18634   pi    2u     sock        0,8      0t0 2903508 protocol: TCPv6
vsftpd  18634   pi    3w      REG      179,2    37351    1427 /var/log/vsftpd.log.1
vsftpd  18634   pi    4r  unknown                             /mnt/gdrive/ACG/TV/202007/沒落要塞 DECA-DENCE/[Airota][Deca-Dence][05][1080p AVC AAC][CHT].mp4 (stat: Permission denied)
vsftpd  18634   pi    5u     unix 0x97115dd6      0t0 2898890 type=STREAM
vsftpd  18634   pi    6u     sock        0,8      0t0 2907724 protocol: TCPv6
```

這邊查了一下

> 產生D狀態的原因出現uninterruptible sleep狀態的進程一般是因為在等待IO，例如磁盤IO、網絡IO等。在發出的IO請求得不到相應之後，進程一般就會轉入uninterruptible sleep狀態，例如若NFS服務端關閉時，如果沒有事先amount相關目錄。在客戶端執行df的話就會掛住整個會話，再用ps axf查看的話會發現df進程狀態位已經變成D。

[Linux 進程的 Uninterruptible sleep(D) 狀態 - Still water run deep - 開發者的網上家園](https://www.cnblogs.com/xiaotengyi/p/7644336.html)

vmstat 可以看到 第二個 b 確實我顯示有 1(可參照上面)

> 由於只有運行狀態的進程才可以接受信號，所以處於uninterruptible sleep的進程，是無法用kill命令殺掉的，即使是加9或15的信號。

但要該怎麼處理呢?


> 正是因為得不到IO的響應，進程才進入了uninterruptible sleep狀態，所以要想使進程從uninterruptible sleep狀態恢復，就得使進程等待的IO恢復，比如如果是因為從遠程掛載的NFS卷不可訪問導致進程進入uninterruptible sleep狀態的，那麼可以通過恢復該NFS卷的連接來使進程的IO請求得到滿足，除此之外，要想幹掉處在D狀態進程就只能重啟整個Linux系統（D進程並不能通過kill 和kill -9 殺掉） 。
> 作者：不穿格子衫的程序猿
> 链接：https://www.jianshu.com/p/8f29d6d70fd6
> 来源：简书
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

[進程查看命令 - 簡書](https://www.jianshu.com/p/8f29d6d70fd6)

### 亂打亂撞解決

網路上說無法kill強制刪掉
這邊我決定 kill 掉看看
一般 kill 無法
但 kill -9 就刪掉了...
這邊就不繼續探討了

top wa 就正常了...

簡單回顧重點

vmstat 第二個 b 可以先確認 uninterruptible sleep程式
再尋找 uninterruptible sleep 程式做處理