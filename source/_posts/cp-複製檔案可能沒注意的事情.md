---
title: cp 複製檔案可能沒注意的事情
date: 2020-01-10 22:06:38
tags: [cp, linux]
categories: Linux
---

最近由於程式做很多檔案處理
就在想 cp 到目的資料夾裡面 ls 是否能看到資料
不過原本猜應該不會
但實驗結果卻不是我想得這樣
最後感覺我們被 GUI 介面受很多影響
~~其實是無聊探討一些不重要的事情(哈哈)~~

<!--more-->

## 產生大檔案

由於我用 PI 效能較差的電腦產生大檔案
```bash
dd if=/dev/zero of=file.txt count=1024 bs=1024    # 產生 1MB
dd if=/dev/zero of=file.txt count=1024000 bs=1024 # 產生 1G
```
參考: [How To Quickly Generate A Large File On The Command Line (With Linux) - Skorks](https://skorks.com/2010/03/how-to-quickly-generate-a-large-file-on-the-command-line-with-linux/)

## 簡單測試

其實就是把大檔案複製到別的資料夾

這邊先說正常**沒中斷**複製檔案中
目的資料夾檔案可以`ls`出來!!!
在想處理中檔案是否有別的方法隱藏
[scp - Is there a way to tell if a file is done copying? - Super User](https://superuser.com/questions/45224/is-there-a-way-to-tell-if-a-file-is-done-copying)
有找到更好方式在做紀錄

1. 有趣結論 1
不過，我 `Ctrl+C` 竟然還會存在
這有點出乎我的意料
平常我們用 Window 桌面(不管是不是Linux)複製中檔案案取消都目的資料夾檔案一定會不見!!!

2. 有趣結論 2
不管事做`kill -9`或`kill -1`都是不會刪除...

3. 有趣結論 3
cp 檔案中...在別的 command line 做刪除動作
竟然 cp command line 指令不會中斷跳出錯誤...

但我想這種種跡象跟 Linux 檔案系統有關係
回想過去我們傳 FTP 跟 轉影音檔到一半中斷
我們檔案並不會刪除
我們在 Window 複製檔案中，我們 GUI 介面可以看到檔案圖示
但按取消後，圖示會跟著刪除
這我想是作業系統有幫我們做刪除動作


## 最後 mv

我在想 mv 應該也是類似這樣
~~cp 完到別的地方，會做刪除動作~~馬上打臉
答案是錯的，同一個 HD 會很快(這應該跟作業系統有關係)

但不同 HD 跟我 cp 實驗一模一樣!!!
中斷會留檔案
至於 window 系統我就不研究了

~~又研究沒什麼軟用的實驗了~~


不相關彩蛋
預計 jQuery 和 inotify-tools 和 DinD(Docker in Docker) & RabbitMQ 要記錄...(我還記得的話之後會寫...怕忘記)
* [Examples using the Docker Engine SDKs and Docker API | Docker Documentation](https://docs.docker.com/develop/sdk/examples/)
* [[docker]privileged参数_docker,privileged_追寻神迹-CSDN博客](https://blog.csdn.net/halcyonbaby/article/details/43499409)
* [Make jQuery :contains Case-Insensitive | CSS-Tricks](https://css-tricks.com/snippets/jquery/make-jquery-contains-case-insensitive/)
* [jquery :contains case-Insensitive - JSFiddle](https://jsfiddle.net/bipen/dyfRa/)
* [inotify-tools使用方法介绍-Linux运维日志](https://www.centos.bz/2012/06/inotify-tools-introduction/)
* [CentOS 6.9上inotify-tools 安装及使用方法-Linux运维日志](https://www.centos.bz/2018/01/centos-6-9%E4%B8%8Ainotify-tools-%E5%AE%89%E8%A3%85%E5%8F%8A%E4%BD%BF%E7%94%A8%E6%96%B9%E6%B3%95/)
* [rsync + inotify-tools 实现 Linux 文件双向实时同步 – Jacky's Blog](https://jackyu.cn/tech/rsync-plus-inotify-tools/)
* [PHP用inotify扩展监控文件_chunyuan314的博客-CSDN博客](https://blog.csdn.net/chunyuan314/article/details/81750211)
* [inotifywait命令_Linux inotifywait 命令用法详解：异步文件系统监控机制](https://man.linuxde.net/inotifywait)
* [Redis 阻塞、安全队列 BLPOP/BRPOPLPUSH - Silverd](http://silverd.cn/2015/07/06/blpop.html)
* [How To Quickly Generate A Large File On The Command Line (With Linux) - Skorks](https://skorks.com/2010/03/how-to-quickly-generate-a-large-file-on-the-command-line-with-linux/)
* [inotify-tools + php脚本实现Linux服务器文件监控并邮件提醒 - 黄小涛 - 博客园](https://www.cnblogs.com/spareribs/p/7722453.html)
* [ktbyers/netmiko: Multi-vendor library to simplify Paramiko SSH connections to network devices](https://github.com/ktbyers/netmiko)
* [python獲取程式執行檔案路徑方法 - IT閱讀](https://www.itread01.com/content/1546932986.html)