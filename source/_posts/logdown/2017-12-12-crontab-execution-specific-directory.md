---
layout: post
title: 'Crontab 執行特定目錄'
date: 2017-12-12 15:05
comments: true
categories: Linux
tags: [crontab]
---
```crontab -l
50 * * * * cd /home/me/.flexget && sh /home/me/.flexget/gitpull.sh >/dev/null 2>&1
```
假如前面沒有做cd動作，執行目錄不會是在相同目錄
在此筆記，以後就不用查了
直接看筆記XD

<!--more-->


在鳥哥看到`12.2.2 script 的執行方式差異 (source, sh script, ./script)`
相信可能跟這個也很有關係

[摩托學園討論區 • 檢視主題 - [分享] 研究 2>&1 後的一點點心得](https://moto.debian.tw/viewtopic.php?f=11&t=17208)
也看到`>/dev/null`執行正確不會東西，但是執行錯誤會顯示在console上面
但是`2>&1`會把錯誤資訊鳥在/dev/null (前提看你前面寫什麼)
詳細看裡面教學

**2020-04-25**

```bash
# 輸出結果也不太一樣
ls  > dirlist  2>&1
ls 2>&1 > dirlist
# 對於第二種情況，標準錯誤只是使用了標準輸出的文件表項，而標準輸出使用了 dirlist 的文件表項。
```
參考:[Bash中的IO與重定向 | 退思園](https://gowa.club/Linux-Unix/Bash%E4%B8%AD%E7%9A%84IO%E4%B8%8E%E9%87%8D%E5%AE%9A%E5%90%91.html)

[What is the 'working directory' when cron executes a job? - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/38951/what-is-the-working-directory-when-cron-executes-a-job)
[unix - Crontab - Run in directory - Stack Overflow](https://stackoverflow.com/questions/8899737/crontab-run-in-directory)
[鳥哥的 Linux 私房菜 -- 第十二章、學習 Shell Scripts](http://linux.vbird.org/linux_basic/0340bashshell-scripts.php#some_ex_run)
[摩托學園討論區 • 檢視主題 - [分享] 研究 2>&1 後的一點點心得](https://moto.debian.tw/viewtopic.php?f=11&t=17208)