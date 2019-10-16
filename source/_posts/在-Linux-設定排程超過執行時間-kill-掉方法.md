---
title: 在 Linux 設定排程超過執行時間 kill 掉方法
date: 2019-10-05 19:30:43
tags: [linux,kill]
categories: Linux
---

最近有一隻排程有時候會卡死
但在想有沒有 crontab 設定方法
網路上爬有爬到解決方法
使用 timeout 指令

<!--more-->

使用方法很簡單

```bash
timeout 10 ping 127.0.0.1
```

詳細了解
 
> timeout [選項] 數字[後綴] 命令 [參數]...
> 
> 後綴」s」代表秒(默認值)，」m」代表分，」h」代表小時，」d」代表天。
> 選項詳解
> 
> 長選項必須使用的參數對於短選項時也是必需使用的。
>   -s, --signal=信號
>         指定在超時時發送的信號。信號可以是類似"HUP"的信號名或是信號數。
>         查看"kill -l"以獲得信號列表
>       --help        顯示此幫助信息並退出
>       --version        顯示版本信息並退出
> 
> 如果程序超時則退出狀態數為124，否則返回程序退出狀態。
> 如果沒有指定信號則默認為TERM 信號。TERM 信號在進程沒有捕獲此信號時殺死進程。
> 對於另一些進程可能需要使用KILL (9)信號，當然此信號不能被捕獲。
> ————————————————
> 版权声明：本文为CSDN博主「Summer_ZJU」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
> 原文链接：https://blog.csdn.net/xiaqunfeng123/article/details/54315390


[Linux命令——timeout - 夏天的风 - CSDN博客](https://blog.csdn.net/xiaqunfeng123/article/details/54315390)

## 子程序 kill 探討

我好奇子程序會不會關掉[如何在 Linux 殺掉(kill)子程序 | 程式狂想筆記](https://malagege.github.io/blog/2019/10/03/%E5%A6%82%E4%BD%95%E5%9C%A8-Linux-%E6%AE%BA%E6%8E%89-kill-%E5%AD%90%E7%A8%8B%E5%BA%8F/)
自從上次紀錄
我好奇子程序會不會關掉??
經過實驗結果**會**
但很奇怪我好奇他怎麼會 kill 掉 子程序
經過我猜測，背景應該是使用`kill -- -$PGID`指令
所以子程序才會關掉

## 其他小記

### 安裝timeout指令

安裝timeout指令(通常沒例外的時候，Linux應該會有)
coreutils
[timeout --> command not found in linux server - Stack Overflow](https://stackoverflow.com/questions/20548460/timeout-command-not-found-in-linux-server)

### kill -2 就使 Ctrl + c

這一樣子程序都會被 kill 掉

### nohup意思

#### 有關 nohup
nohup 的全寫是 “no hangup”, HUP hangup (HUP) 訊號會在使用者登出時, 系統向 process 發出, 通知 process 結束, 但透過 nohup 執行的指令, nohup 會將 HUP 訊息截取, 讓指令可以繼續執行。
[nohup 指令 - 背景執行指令](https://www.opencli.com/linux/nohup-background-execute-command)

掛起信號【hang up】，終端斷線，經常在退出系統前使用，會終止進程。但是，一般啟動程序時為了讓程序繼續運行，會指定 nohup 就是為了不讓程序接收掛起信號而終止，這樣在退出系統時程序仍舊能正常運行
[Linux 之 kill 命令入門實踐 | 蝦丸派](https://www.playpi.org/2019042101.html)

參考連結:
[Ways to kill parent and child processes in one command - Fibrevillage](http://fibrevillage.com/sysadmin/237-ways-to-kill-parent-and-child-processes-in-one-command)
[Linux 中 Kill -2 和 Kill -9 的区别 - Harry的专栏 - CSDN博客](https://blog.csdn.net/Harry_lyc/article/details/53543928)
[Linux中kill -2、kill -9等区别 && kill signal汇总 - 天涯的浪子 - CSDN博客](https://blog.csdn.net/a1010256340/article/details/75253353)
[Linux 之 kill 命令入门实践 | 虾丸派](https://www.playpi.org/2019042101.html)
[Linux kill -9 和 kill -15 的区别 - 妖老山黑 - 博客园](https://www.cnblogs.com/liuhouhou/p/5400540.html)
[process - ps switches to display PID, PPID, PGID, and SID collectively - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/82724/ps-switches-to-display-pid-ppid-pgid-and-sid-collectively)
[kill -HUP pid - 小菜鸟的天地 - CSDN博客](https://blog.csdn.net/zhuying_linux/article/details/7031573)
[Linux命令——timeout - 夏天的风 - CSDN博客](https://blog.csdn.net/xiaqunfeng123/article/details/54315390)
[Running php script as cron job - timeout issues? - Stack Overflow](https://stackoverflow.com/questions/13352388/running-php-script-as-cron-job-timeout-issues)