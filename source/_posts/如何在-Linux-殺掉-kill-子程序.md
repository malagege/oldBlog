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

<!--more-->

## 什麼是子程序

可以先去鳥哥那邊了解子程序運作原理
簡單說目前看到有兩種

1. 父程序呼叫子程序，父程序會掛起(類似stop)，不會做任何動作，等子程序跑完才會往下執行
PS: 剛好最近了解阻塞跟非阻塞，這應該就是`阻塞`

2. fork and exec：程序呼叫的流程(可參考:[鳥哥的 Linux 私房菜 -- 第十六章、程序管理與 SELinux 初探](http://linux.vbird.org/linux_basic/0440processcontrol.php)，我目前還沒深入研究)


## kill 父程序實驗

其實也能正常關閉kill -15 預設就是`15`

kill 指令會殺掉程序但不會殺掉子程序

kill -9 $pid

bash(3625)───sh(6933)───sh(6934)───sleep(6935)

可以開兩個 command line 去做實驗
因為使用 echo $$ 可以看當下的bash pid

可以用 pstree -p 3625
`kill -9 6934`
查看當下子程序，但刪掉父程序不能用`pstree -p 6934` 去看(這邊有空看有沒有比較方便的方法觀察)
需要看6934有沒有刪掉，**結果是沒有刪掉的**

實驗 shell 如下
如:

```a.sh
sh b.sh
sh b.sh
```

```b.sh
time sleep 20
```

題外話
其實我有做一個實驗，直接執行`a.sh` 直接`Ctrl+c`
他會把所有子程序刪掉!!!

我後來發現 `kill -2 $PID` 就是做 `Ctrl+c`
但這一段不確定是否正確刪除方式

可以使用 pkill -P $pid 刪掉所有子程序(我網頁看到是小寫 -p)


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

