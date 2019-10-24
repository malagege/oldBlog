---
title: Shell Script 小記
date: 2019-10-23 21:30:24
tags:
categories:
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

## 相關連結
[Shell——你只需要了解这么多 - 枣面包的博客 - CSDN博客](https://blog.csdn.net/weixin_37490221/article/details/80869792)
[[Shell Script] Day27-提高可讀性之函式寫法(三) - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10138639)
[Linux Shell函数返回值 - 阳光岛主 - CSDN博客](https://blog.csdn.net/ithomer/article/details/7954577)