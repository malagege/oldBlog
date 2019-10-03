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