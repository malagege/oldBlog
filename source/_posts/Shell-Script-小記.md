---
title: Shell Script 小記
date: 2019-10-23 21:30:24
tags: [shell,sh,linux]
categories: Linux
---

## shell 小記

<!--more-->

### 變數大寫

```bash
TODAY=`date +"%Y%m%d"`
YESTERDAY=`date +"%Y%m%d"  --date=${TODAY}-1day`
```

## 變數宣告 = 不能有空白

```bash
A = 1234 # A: command not found
B=1234   # Success
```

### 字串截取

```bash
STRING="123456789"
echo ${STRING:2:5}   # 34567
```


## Array 宣告

```bash
ARR=(123 456 789)
echo ${ARR[2]}  # 789
echo ${#ARR[@]}  # Array length

for i in "${ARR[@]}"
do
   echo "$i"
   # or do whatever with individual element of the array
done
```

## Array join(合併) 動作

```bash
function join_by { local d=$1; shift; echo -n "$1"; shift; printf "%s" "${@/#/$d}"; }
```

[How can I join elements of an array in Bash? - Stack Overflow](https://stackoverflow.com/questions/1527049/how-can-i-join-elements-of-an-array-in-bash)


## if 條件判斷

```bash
if [  ${#ERROR_ARR[@]} != 0 ]
then
    echo xxxx
fi
```

### 多個條件判斷

```bash
if [ -e $FIND_FILE_320 ] && [ -e $FIND_FILE_192 ] && [ -e $FIND_FILE_30 ] && [  -e $FIND_FILE_ALBUM ]
then
    echo xxx
fi
```

## mysql 查詢寫回變數

FIND_ARRAY=(`mysql -h xxx.xx.xx.xx -D xxxx -u xxx -p__password__ -se "SELECT xxx FROM xxx WHERE  limit 100"`)

* [shell处理mysql增、删、改、查 - 奔跑的Man - 博客园](https://www.cnblogs.com/iforever/p/4459857.html)
* [shell中读写mysql数据库 | 赖明星](http://mingxinglai.com/cn/2013/01/use-mysql-in-shell/)
* [bash - How concatenate strings with a newline - Stack Overflow](https://stackoverflow.com/questions/49819640/how-concatenate-strings-with-a-newline)

```bash
MYSQL_CMD="/usr/bin/mysql -u ga_user -pxxx -h xxx --database=xxx -s"
FIND_ARRAY=(`echo "SELECT xxx FROM xxxx WHERE ... and ..... < DATE_ADD(now(),INTERVAL -3 DAY) ORDER BY id LIMIT 100" | ${MYSQL_CMD}`)

FIND_ARRAY=(`mysql -h xxx -D xxx -u xxx -pxxx -se "SELECT xxx FROM xxx WHERE xxx and DATE_ADD(now(),INTERVAL -3 DAY) LIMIT 100"`)
```

## function 宣告
```bash
function join_by { local d=$1; shift; echo -n "$1"; shift; printf "%s" "${@/#/$d}"; }
ERROR_SONG_ID_STRING=$(join_by , ${ERROR_SONG_ID[@]})
join_by , 1 2 3  
```

### function 還傳段行字串
```bash
#!/bin/bash
a_function() {
    echo -e "Hello\nworld\n 1"
    echo "Hello"
    echo "world"
    echo "Hello\nworld 4"
}

echo -e "Hello\nworld\n 1"
echo "Hello"
echo "world"
echo "Hello\nworld 4"

x=$(a_function "x")
echo "x-no-quotes>"
echo $x                       #No new lines!
echo "<x-no-quotes"

```
[scripting - How to debug a bash function that returns a value, and how to add newlines to a variable? - Stack Overflow](https://stackoverflow.com/questions/27872069/how-to-debug-a-bash-function-that-returns-a-value-and-how-to-add-newlines-to-a/27872114)

### shell script 布林運算順序

[shell script 布林運算順序 | 程式狂想筆記](http://localhost:4000/blog/2019/11/06/shell-script-%E5%B8%83%E6%9E%97%E9%81%8B%E7%AE%97%E9%A0%86%E5%BA%8F/)

## 有關 * 

[linux bash / shell 有關 * 星號的事情 | 程式狂想筆記](https://malagege.github.io/blog/2020/01/21/linux-bash-shell-%E6%9C%89%E9%97%9C-%E6%98%9F%E8%99%9F%E7%9A%84%E4%BA%8B%E6%83%85/)

## shell 比較字串外面需要包

> if 常常遇到`[: ==: unary operator expected`
> 
> 那是因為 == 左邊沒有抓到值，或是值為 null
> 
> 有兩種解決方法：
> * if [ "$app-running" == "" ];
> * if [[ $app-running == "" ]];

參考:[Linux 的 shell script 中，遇到 unary operator expected 的解決方法](https://medium.com/micheh/linux-%E7%9A%84-shell-script-%E4%B8%AD-%E9%81%87%E5%88%B0-unary-operator-expected-%E7%9A%84%E8%A7%A3%E6%B1%BA%E6%96%B9%E6%B3%95-3b4725d3fdf1)

## 變數運算

最近使用 shell 做回圈做`${index+1}`
結果沒有印出來
爬文有找到幾個方法

1. declare -i來聲明整數變量
```bash
x=1
x+=1
echo $x
# 2
```

2. 使用let命令

```bash
i=1
let i+=1
echo $i
# 2
```

3. 使用(())

```bash
i=1
((++i))
echo $i
# 2
```

4. 使用expr命令

```bash
i=1
i=`expr $i + 1`
echo $i
# 2
```

5. 使用$(())

```bash
i=1
i=$(($i + 1))
echo $i
# 2
```

6. 使用$[]

```bash
i=1
i=$[$i + 1]
echo $i
# 2
```

我比較喜歡 $(()) 和 (())

參考來源: [Shell中整数自增的几种方式_杰瑞的专栏-CSDN博客](https://blog.csdn.net/Jerry_1126/article/details/52336340)

## 複製檔案

[cp 複製檔案可能沒注意的事情 | 程式狂想筆記](https://malagege.github.io/blog/2020/01/10/cp-%E8%A4%87%E8%A3%BD%E6%AA%94%E6%A1%88%E5%8F%AF%E8%83%BD%E6%B2%92%E6%B3%A8%E6%84%8F%E7%9A%84%E4%BA%8B%E6%83%85/)

## shell function 回傳只能數字

一般我們看 shell script function return 回傳值只能是數字
沒法回傳字串，仔細想想也滿有道理的
因為正常程式執行結束會回傳 exit (int)
要確定程式是否有正確執行完成 正常執行完成會回傳0

那有沒有回傳字串方法
我看到是用 function 裡面做 echo 
外面用`這個符號去包
滿有趣的寫法


## init.d 守護進程

- [Run a Java Application as a Service on Linux - Stack Overflow](https://stackoverflow.com/questions/11203483/run-a-java-application-as-a-service-on-linux)

- [迷途工程師: Init Script for OpenWrt，新增一個自己的init script](http://dannysun-unknown.blogspot.com/2017/02/init-script-for-openwrtinit-script.html)

- [init-script-template/template at master · fhd/init-script-template](https://github.com/fhd/init-script-template/blob/master/template)
上面有用 ps -p 指令
- [最全的Linux進程監視命令ps的30個常用例子 - 每日頭條](https://kknews.cc/zh-tw/code/4pnr3o2.html)

- [PHP守护进程 - InfoQ 写作平台](https://xie.infoq.cn/article/c3a40c88f95d3a1cb56045dc4)

- [Linux 守护进程原理及实例（Redis、Nginx）_杨博东的博客-CSDN博客_redis守护进程](https://blog.csdn.net/yangbodong22011/article/details/78650896)
- [linux - Bash: wait with timeout - Stack Overflow](https://stackoverflow.com/questions/10028820/bash-wait-with-timeout)


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

參考:[shell - bash trap interrupt command but should exit on end of loop - Stack Overflow](https://stackoverflow.com/questions/26808727/bash-trap-interrupt-command-but-should-exit-on-end-of-loop)

trap 相關指令，可以用來可以控制關閉方法
- [优雅地结束docker容器的init bash脚本](http://coinfaces.me/posts/gracefully-stop-docker-init-bash-script/)
- [shell-trap | 平凡備忘錄](http://blog.gitdns.org/2016/08/29/shell-trap/)
- [Bash 程式設計教學：平行執行背景子行程，用 wait 等待工作結束 - G. T. Wang](https://blog.gtwang.org/programming/bash-tutorial-parallel-subprocesses-and-wait/)



## 相關連結
[Shell——你只需要了解这么多 - 枣面包的博客 - CSDN博客](https://blog.csdn.net/weixin_37490221/article/details/80869792)
[[Shell Script] Day27-提高可讀性之函式寫法(三) - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10138639)
[Linux Shell函数返回值 - 阳光岛主 - CSDN博客](https://blog.csdn.net/ithomer/article/details/7954577)