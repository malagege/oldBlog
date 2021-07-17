---
title: Shell 實作守護進程方法
date: 2021-01-28 21:36:17
tags: shell
categories: Linux
---

Shell 實作守護進程方法

<!--more-->

## 查詢 init.d 守護進程

- [Run a Java Application as a Service on Linux - Stack Overflow](https://stackoverflow.com/questions/11203483/run-a-java-application-as-a-service-on-linux)

- [迷途工程師: Init Script for OpenWrt，新增一個自己的init script](http://dannysun-unknown.blogspot.com/2017/02/init-script-for-openwrtinit-script.html)

- [init-script-template/template at master · fhd/init-script-template](https://github.com/fhd/init-script-template/blob/master/template)
上面有用 ps -p 指令
- [最全的Linux進程監視命令ps的30個常用例子 - 每日頭條](https://kknews.cc/zh-tw/code/4pnr3o2.html)

- [PHP守护进程 - InfoQ 写作平台](https://xie.infoq.cn/article/c3a40c88f95d3a1cb56045dc4)

- [Linux 守护进程原理及实例（Redis、Nginx）_杨博东的博客-CSDN博客_redis守护进程](https://blog.csdn.net/yangbodong22011/article/details/78650896)
- [linux - Bash: wait with timeout - Stack Overflow](https://stackoverflow.com/questions/10028820/bash-wait-with-timeout)

- [springboot 打包（jar）部署在 Linux 環境 - SegmentFault 思否](https://segmentfault.com/a/1190000022686414) <--  覺得這個寫得很完整

## 記錄網路上找的不錯寫法

以下參考[springboot 打包（jar）部署在 Linux 環境 - SegmentFault 思否](https://segmentfault.com/a/1190000022686414) 
這個應該也能改成一般程式執行

**注意!!這邊不是無限loop**
估記是Java 程式做 loop

```sh start.sh
#!/bin/bash

AppName=ruoyi.jar

#JVM參數
JVM_OPTS="-Dname=$AppName  -Duser.timezone=Asia/Shanghai -Xms512M -Xmx512M -XX:PermSize=256M -XX:MaxPermSize=512M -XX:+HeapDumpOnOutOfMemoryError -XX:+PrintGCDateStamps  -XX:+PrintGCDetails -XX:NewRatio=1 -XX:SurvivorRatio=30 -XX:+UseParallelGC -XX:+UseParallelOldGC"
APP_HOME=`pwd`
LOG_PATH=$APP_HOME/logs/$AppName.log

if [ "$1" = "" ];
then
    echo -e "\033[0;31m 未輸入操作名 \033[0m  \033[0;34m {start|stop|restart|status} \033[0m"
    exit 1
fi

if [ "$AppName" = "" ];
then
    echo -e "\033[0;31m 未輸入應用名 \033[0m"
    exit 1
fi

function start()
{
    PID=`ps -ef |grep java|grep $AppName|grep -v grep|awk '{print $2}'`

    if [ x"$PID" != x"" ]; then
        echo "$AppName is running..."
    else
        nohup java -jar  $JVM_OPTS target/$AppName > /dev/null 2>&1 &
        echo "Start $AppName success..."
    fi
}

function stop()
{
    echo "Stop $AppName"
    
    PID=""
    query(){
        PID=`ps -ef |grep java|grep $AppName|grep -v grep|awk '{print $2}'`
    }

    query
    if [ x"$PID" != x"" ]; then
        kill -TERM $PID
        echo "$AppName (pid:$PID) exiting..."
        while [ x"$PID" != x"" ]
        do
            sleep 1
            query
        done
        echo "$AppName exited."
    else
        echo "$AppName already stopped."
    fi
}

function restart()
{
    stop
    sleep 2
    start
}

function status()
{
    PID=`ps -ef |grep java|grep $AppName|grep -v grep|wc -l`
    if [ $PID != 0 ];then
        echo "$AppName is running..."
    else
        echo "$AppName is not running..."
    fi
}

case $1 in
    start)
    start;;
    stop)
    stop;;
    restart)
    restart;;
    status)
    status;;
    *)

esac

```
另外一種
看試強制關閉
```sh start_init.sh
#!/bin/sh
JAR_NAME=ruoyi.jar

tpid=`ps -ef|grep $JAR_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
if [ ${tpid} ]; then
echo 'Stop Process...'
fi
sleep 5
tpid=`ps -ef|grep $JAR_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
if [ ${tpid} ]; then
echo 'Kill Process!'
kill -9 $tpid
else
echo 'Stop Success!'
fi

tpid=`ps -ef|grep $JAR_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
if [ ${tpid} ]; then
        echo 'App is running.'
else
        echo 'App is NOT running.'
fi

rm -f tpid
nohup java -jar ./$JAR_NAME >/dev/null 2>&1 &
echo $! > tpid
echo 'Start Success!'
```

這個應該可以改成一般非 Java 程式排程
再搭配關閉訊號控制程式，是不是就是完美關閉腳本
好吧，其實我還是覺得 Systemd 才是最佳解決方案

## 關閉訊號控制程式

最近有看到可以傳送關閉訊號控制程式跑完進程關閉程式

```sh
#!/bin/bash

mp3convert () {
  echo "mp3convert..."; sleep 5; echo "mp3convert done..."
}

PreTrap() {
  echo "in trap"
  QUIT=1
  echo "exiting trap..."
}

CleanUp() {
  ### Since 'wait $PID' can be interrupted by ^C, we need to protected it
  ### by the 'kill' loop  ==> double/triple control-C problem.
  while kill -0 $PID >& /dev/null; do wait $PID; echo "check again"; done

  ### This won't work (A simple wait $PID is vulnerable to double control C)
  # wait $PID

  if [ ! -z $QUIT ]; then
     echo "clean up..."
     exit
 fi
}

trap PreTrap SIGINT SIGTERM SIGTSTP
#trap CleanUp EXIT

for loop in 1 2 3; do
    (
      echo "loop #$loop"
      mp3convert
      echo magic 1
      echo magic 2
      echo magic 3
    ) &
    PID=$!
    CleanUp
    echo "done loop #$loop"
done

```

測試結果，還真的有用
開兩個 cmd ，一個執行，另一個kill
並不會直接結束

參考:[shell - bash trap interrupt command but should exit on end of loop - Stack Overflow](https://stackoverflow.com/questions/26808727/bash-trap-interrupt-command-but-should-exit-on-end-of-loop)

trap 相關指令，可以用來可以控制關閉方法
- [优雅地结束docker容器的init bash脚本](http://coinfaces.me/posts/gracefully-stop-docker-init-bash-script/)
- [shell-trap | 平凡備忘錄](http://blog.gitdns.org/2016/08/29/shell-trap/)
- [Bash 程式設計教學：平行執行背景子行程，用 wait 等待工作結束 - G. T. Wang](https://blog.gtwang.org/programming/bash-tutorial-parallel-subprocesses-and-wait/)


## 調整完美關閉腳本

TODO: 待修改


彩蛋

[執行 Docker 容器可使用 dumb-init 或 tini 改善程序優雅結束的問題 | The Will Will Web](https://blog.miniasp.com/post/2021/07/09/Use-dumb-init-in-Docker-Container)