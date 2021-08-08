---
title: linux bash / shell 有關 * 星號的事情(glob)
date: 2020-01-21 21:25:57
tags: [bash,shell]
categories: Linux
---

以前 `ls` 萬用字元真的很方便

```
ls *.jpg
```

這樣可以列出很多 `*.jpg` 圖片
但是有些細節沒有釐清
偶爾會遇到不能用的狀況下
今天終於徹底了解

**2021-07-25**
最近才發現這有專有名稱叫做`glob`

<!--more-->

## 釐清 `*`

```bash
echo *
# file list...(ex: xxx.jpg ffff.jpg)
```

**就是現在目錄抓到相關檔案名稱**
這也是為什麼? 原理就這麼簡單
但我這邊沒講更細
下面 shopt -s nullglob 會提到

## shell array 應用


```bash
jpgs=(*.jpg)
```

這樣看似可以用
但沒有 jpg ，就會產生 *.jpg
這邊要小心
這邊也是有辦法解決

```bash
shopt -s nullglob # bash
unsetopt nomatch # zsh


files=(xorg-x11-fonts*)

if [ -e "${files[0]}" ];
then
    printf "BLAH"
fi
```
參考:[sh - Check if a file exists with wildcard in shell script - Stack Overflow](https://stackoverflow.com/questions/6363441/check-if-a-file-exists-with-wildcard-in-shell-script/17404812#17404812)

### shopt -s nullglob

```bash
shopt -s nullglob # enable 
shopt -u nullglob # disable
```


```bash
echo my*file
my*file
shopt -s nullglob
echo my*file # 不會印出東西
```
參考: [linux:以下shell選項的含義是什麼 - shopt -s nullglob \ textglob - Codebug](https://t.codebug.vip/questions-844092.htm)

```bash
#!/bin/sh  
old=$(pwd)  
[ $# -lt 2 ] && echo -e "\t[Info] Give arg1=Folder path; arg2=File extension!\n" && exit 1  
[ -d $1 ] && cd $1 || exit 2  
    
shopt -s nullglob  
found=0  
for i in *.$2; do  
    echo "File $i found" # or take other action  
    found=1  
done  
shopt -u nullglob  
[ $found -eq 0 ] && echo "Directory is empty"  
cd $old  
```

// List all file with extension .mp3 under folder 'empty'
./listFE.sh empty mp3
File test.mp3 found

以上參考:[程式扎記: [Linux 文章收集] Use BASH nullglob To Verify *.c Files Exists or Not In a Directory](http://puremonkey2010.blogspot.com/2016/01/linux-use-bash-nullglob-to-verify-c.html)


當然也有別的解決方案

## test -f *.jpg

我公司有古早程式這樣寫
但會遇到一些問題

除了上述解決方法

還有一種是

```bash
files=$(ls /tmp/*.cache 2> /dev/null | wc -l)
if [ "$files" != "0" ]
then
    echo "Cache files exist: do something with them"
else
    echo "No cache files..."
fi
```

## find 方法(多層搜尋方法)

```bash
files=$(find /tmp -type f -name "*.cache" | wc -l)

if [ "$files" != "0" ]
then
    echo "Cache files exist: do something with them"
else
    echo "No cache files..."
fi
```

find 搜尋也可以加 or 條件
再多個搜尋也是很方便
```
find /tmp -type f -name "*.mpg" -o -name "*.jpg"
```

更多方法可以看:[Bash tips: if -e wildcard file check => \[: too many arguments - MDLog:/sysadmin](http://www.ducea.com/2009/03/05/bash-tips-if-e-wildcard-file-check-too-many-arguments/)


相關連結:

* [bash - Use asterisk in variables - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/378205/use-asterisk-in-variables)
* [sh - Check if a file exists with wildcard in shell script - Stack Overflow](https://stackoverflow.com/questions/6363441/check-if-a-file-exists-with-wildcard-in-shell-script/17404812#17404812)
* [How to Check / Test if a File or Directory Exists in Bash](https://phoenixnap.com/kb/how-to-check-if-file-or-directory-exists-bash-shell)
* [Bash tips: if -e wildcard file check => \[: too many arguments - MDLog:/sysadmin](http://www.ducea.com/2009/03/05/bash-tips-if-e-wildcard-file-check-too-many-arguments/)
* [電腦各種符號英文唸法 | 阿維雜記本 (Wei's Blog)](https://blog.hsdn.net/970.html)
* [linux - bash, detecting mp4 files - Stack Overflow](https://stackoverflow.com/questions/25224324/bash-detecting-mp4-files)
* [关于bash：确认是否存在一个或多个目录 | 码农家园](https://www.codenong.com/10653300/)