---
title: 如何在 Linux 殺掉(kill)子程序
date: 2019-10-03 20:50:49
tags: [kill,pkill]
categories: Linux
---

最近有排程卡住
MIS 殺掉(kill)程式，發現他的子程序還在跑
我們都以為 kill 掉父程序就可以關掉子程序
今天實驗出來是不能的

2021 沒想到這篇 Google 還滿容易搜尋到
當初只是操作筆記，簡單紀錄
不過有部分寫錯，把多餘錯誤份刪掉
留下重點...避免其他人吸收錯誤知識

<!--more-->

## 什麼是子程序

可以先去鳥哥那邊了解子程序運作原理
簡單說目前看到有兩種

1. 父程序呼叫子程序，父程序會掛起(類似stop)，不會做任何動作，等子程序跑完才會往下執行
PS: 剛好最近了解阻塞跟非阻塞，這應該就是`阻塞`

2. fork and exec：程序呼叫的流程(可參考:[鳥哥的 Linux 私房菜 -- 第十六章、程序管理與 SELinux 初探](http://linux.vbird.org/linux_basic/0440processcontrol.php)，我目前還沒深入研究)


## kill 父程序實驗

**只要在 kill -$PGID**
**只要在 kill -$PGID**
**只要在 kill -$PGID**

`kill -$PID`
其實下面我之前引用英文文章有寫到


這邊我之前測試，使用 Ctrl + C 想說就是 `kill -2 xxx`
但我阿現實作跟 kill 有差異`，我測試結果子程序會刪除
這邊就不深入探討了...
[Linux ctrl-c explained(ctrl-c 详解) - SegmentFault 思否](https://segmentfault.com/a/1190000018369650)



**2019-10-05**
原來我誤會了
其實 kill 也能殺掉`子程序`
在研究 timeout 指令，實驗子程序狀況發現子程序也會關掉
這又是另外一個事情了

推薦使用下面第二點方法去 kill 掉
```bash
# 下面兩個寫法一樣
# 另外 $PGID 可解讀就是父層可以被 PID
kill -- -$PGID
kill -15 -$PGID
# 當然也可以強制關閉
kill -9 -$PGID
```

PGID 也可以用 ps 指令去查
[process - ps switches to display PID, PPID, PGID, and SID collectively - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/82724/ps-switches-to-display-pid-ppid-pgid-and-sid-collectively)

**2020-11-26**
最近用碰觸 kill
忘記一般 kill 要加負號才能刪掉子程序

最近也在探討 nohup 和 & 也能不能做尋組刪除
答案是可以的

可以參考: [[譯] 如何殺死一個進程和它的所有子進程](https://juejin.cn/post/6844903927989665806?utm_campaign=devtool.com&utm_medium=devtool.com&utm_medium=devtool.com&utm_source=devtool.com%3Futm_campaign%3Ddevtool.com&utm_source=devtool.com) {% asset_link web1.png 備份圖 %}

最近想做一個守護進程...
再研究...

參考連結:

> 1.kill a group of processes with negative PID(Process ID)
> 
> kill  -TERM -PID
> 
> Is to kill PID and all its child processes.
> 2. kill a group of processes with their PGID(Process Group ID)
> 
> kill -- -$PGID   Kill using the default signal (TERM = 15)
> kill -9 -$PGID   Kill using the KILL signal (9)
> 
> 3. kill a group processes with only PID info
> 
> kill -- -$(ps -o pgid= $PID | grep -o [0-9]*)
> 
> Actually, you may notice that it's just the way from #2
> 4.Using pkill, kill processes by PGID(Proess Group ID)
> 
> pkill -9 -g $PGID
> 
> 5.Using pkill, kill processes by GID(Group ID)
> 
> pkill -9 -G $GID
> 
> 6.Using pkill, kill processes by PPID(Parent Process ID)
> 
> pkill -9 -p $PPID
> 
> 7.Using pkill, kill processes by terminal
> 
> pkill -9 -t $terminal
> 
> Note: without /dev/ prefix
> 8.Using pkill, kill processes by process name
> 
> pkill -9 -x $process_name
> 
> 9.Using pkill, kill processes by session
> 
> pkill -9 -s $sess
> 
> How to get PID,PGID,sessionid etc?
> ```
> # ps -o pid,ppid,pgid,gid,sess,cmd -U root
>   PID  PPID  PGID   GID  SESS CMD
> ```
 
* [Ways to kill parent and child processes in one command - Fibrevillage](http://fibrevillage.com/sysadmin/237-ways-to-kill-parent-and-child-processes-in-one-command)
* [鳥哥的 Linux 私房菜 -- 第十六章、程序管理與 SELinux 初探](http://linux.vbird.org/linux_basic/0440processcontrol.php#ps)
* [linux - Best way to kill the all child processes in Linux - By Microsoft Award MVP - ubuntu - red hat - debian - linux server - linux pc - Learn in 30sec | wikitechy](https://www.wikitechy.com/tutorials/linux/best-way-to-kill-all-child-processes-in-linux)
* [（三）进程各种id：pid、pgid、sid、全局pid、局部pid - win9 - CSDN博客](https://blog.csdn.net/qq_33160790/article/details/81346663)
* [阿旺的 Linux 開竅手冊-process 進程(行程/程序)](http://www.polish.url.tw/ach8/ach8.html#pgid)
* [process - Kill all descendant processes - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/124127/kill-all-descendant-processes)
* [阿旺的 Linux 開竅手冊-process 進程(行程/程序)](http://www.polish.url.tw/ach8/ach8.html#kill)
* [子进程及时知道父进程已经退出的最简单方案 - 横云断岭的专栏 - CSDN博客](https://blog.csdn.net/hengyunabc/article/details/5921749)