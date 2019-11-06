---
title: flock 防止重新執行方法
date: 2019-08-19 22:07:54
tags: [flock,linux]
categories: Linux
---

印象中以前寫 PHP 有用過 flock
但這只用在簡單 PHP 上面
最近看到flock指令
突然想啟以前寫 PHP
有用過這個

<!--more-->

[RPubs - Linux 小撇步：利用flock來做同步和非同步應用](https://rpubs.com/wush978/flock) {% asset_link web3.png 備份圖 %}

裡面有提到flock 基本用法
```sh
#! /bin/bash
sleep 10
date
```
> 儲存成test.sh並且打開執行權限（chmod 700 test.sh）
> 此時如果我們打開兩個shell, 並且約同時執行：

> flock /tmp/demo.lock /tmp/test.sh

簡單測試用兩個command line 測試
但我還不確定有沒有照順序執行

我有簡單開三個command line 測試
確定不會照順序執行

flock 預設為`-x` 

`-c` 後面下 command line，通常放在最後面


`-x` exclusive lock

`-s` shared lock

`-n` or `-xn` or `-sn`
~~當下有執行程式(錯誤說法)~~，不會執行程式

有趣的xxx.lock檔案一直都會在!!!

簡單來說就是當 shared lock，別的程式 (shared lock) 還是能執行，但是 exclusive lock 不行
反之
當 exclusive lock 程式執行， shared lock 和 exclusive lock 都不能執行!!!

可以開三個 command line 玩玩看

```
flock -s  /tmp/demo.lock /tmp/test.sh
flock -x  /tmp/demo.lock /tmp/test.sh
flock -sn  /tmp/demo.lock /tmp/test.sh
flock -xn  /tmp/demo.lock /tmp/test.sh
```
## linux lock 種類

* shared lock

* exclusive lock

> 同時持有一個shared lock的process數量並沒有限制. 但同時只有一個process有exclusive lock.(Exclusive lock會拒絕其他shared lock和exclusive lock)
> 無論process對於檔案的存取方式為何(R/W, ReadOnly, WriteOnll), 都可以使用flock()對檔案加上shared lock或exclusive lock
> shared lock跟exclusive lock可以互相轉換. 但轉換的過程不是atomic的. 轉換的步驟是先移除舊的鎖然後建立新的鎖. 中間若有另一個新鎖上去, 轉換過程就會發生失敗並且失去原本的鎖。
> 再將shared lock轉成exclusive lock的過程中. 若有另一個process持有相同的shared lock, 那他就會被blocking, 除非他有設置LOCK_NB

參考: [CODING IS ART: Linux 檔案鎖(File Locking)](http://objectaline19.blogspot.com/2017/03/linux-file-locking.html) {% asset_link web1.png 備份圖 %}


我覺得善用這個特性，寫排成會非常方便
印象中有看過程式適用`ps`去看關鍵字找東西
但透過這個真的會很方便，又不用寫程式!!!

更多設定方法可參考 [Linux 防止 Shell 指令稿重複執行教學 - G. T. Wang](https://blog.gtwang.org/linux/prevent-shell-script-duplicate-executions/) {% asset_link web2.png 備份圖 %}


**2019-10-01**
找到查看 lock 檔案方法
[[Linux] 使用 lsof, lslocks, fuser 指令列出目前鎖定中的 file locks | EPH 的程式日記](https://ephrain.net/linux-%E4%BD%BF%E7%94%A8-lsof-lslocks-fuser-%E6%8C%87%E4%BB%A4%E5%88%97%E5%87%BA%E7%9B%AE%E5%89%8D%E9%8E%96%E5%AE%9A%E4%B8%AD%E7%9A%84-file-locks/)
[process - How to list processes locking file? - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/85994/how-to-list-processes-locking-file)

**2019-10-26**

最近上了 Swoole 課程
最近用 rclone 寫 tranmission 腳本
使用 flock 防止重覆執行
沒有預到問題
但最近我也觀查到 rclone 上傳的時候
~~會開很多`子程序`~~ 結果是執行緒
~~但我沒有預到什麼問題~~ 結果是執行緒 😜

在上 swoole 裡面有提到子程序 fork 主程序
這時候主程序會跟子程序變數做隔離
~~但我很好奇 flock 竟然能讓子程序繼續運作~~ 結果是執行緒 🤷‍
但我覺得 flock 沒辦法測試子程序執行狀況，畢竟是用 flock 指令

上面再搞笑 orz，還好有翻到這篇
補充一下， n 個 thread 都在同一個 process
所以算同一個程序，應該不會有 lock 問題
剛剛看到[Ununtu htop - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/questions/10194441?sc=rss.qu)
~~瞬間被打臉~~，原來綠色不是子程序，但為什麼會有獨立的 PID?
> 綠色字的是 Thread ，你可以在 Setup -> Display options -> Hide userland process thread 選項做調整，看你要不要顯示
> 另外你也可以按 F5 切換 Tree view ，這樣就可以明確的看出來那些綠字的 thread 是屬於哪個 process 的

最後有看到比較詳細的介紹[[筆記]Linux的thread和signal - petertc - Medium](https://medium.com/@petertc/thread-and-signal-in-linux-4f2038d18fd) {% asset_link web4.png 備份圖 %}
> 同process的thread會有相同的process id，且個別有thread id。對於multithread的process，process id又稱為thread group id
> ...
> 圖中Command欄位是綠色代表這是一個thread，其PID欄位值其實是thread id。圖中ipython共有有三個thread（一個leader, 兩個member）

原來是我誤會大了!!難怪怎麼對 thread 做 kill 沒有效果
更多 process 介紹可以看這篇[[筆記]Linux的process和signal - petertc - Medium](https://medium.com/@petertc/session-process-group-and-signal-in-linux-7fbe85c0b0c5)

因為我在 Raspberry PI 觀察
[[Linux] 使用 lsof, lslocks, fuser 指令列出目前鎖定中的 file locks | EPH 的程式日記](https://ephrain.net/linux-%E4%BD%BF%E7%94%A8-lsof-lslocks-fuser-%E6%8C%87%E4%BB%A4%E5%88%97%E5%87%BA%E7%9B%AE%E5%89%8D%E9%8E%96%E5%AE%9A%E4%B8%AD%E7%9A%84-file-locks/)
預設沒有使用 lsof 所以就不測試了
lslock
```
COMMAND           PID  TYPE SIZE MODE  M START END PATH
cron              304 FLOCK      WRITE 0     0   0 /run...
flock           17094 FLOCK      WRITE 0     0   0 /...
(unknown)         278 FLOCK      WRITE 0     0   0 /run...
(unknown)         281 FLOCK      WRITE 0     0   0 /run...
```
不知道我的 PI 怎麼了orz
PATH 看不到

sudo fuser 可以看到 lock 檔案
```
pi@raspberrypi:~ $ sudo fuser /tmp/rclone.lock
/tmp/rclone.lock:    17094 17095
```


[flockを孫プロセスに保持させてプロセスの終了までロックを無理やり保持する](https://gist.github.com/beyu/462241bd73ae630cd7ee937a90766046) 不知道這是什麼?先留一下