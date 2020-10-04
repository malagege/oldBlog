---
title: Linux/Windows 執行腳本抓取當前腳本路徑方法
date: 2020-09-10 23:03:04
tags: [linux,window]
categories: Linux
---

我們很長寫腳本，在任意A目錄當執行`不同的B目錄`的腳本抓取路徑是A
但實務上有時候希望抓到B目錄
最近在想這個有什麼方法

<!--more-->

## Linux

以下都是參考:[Shell Script 取得 Script 所在目錄位置](https://www.opencli.com/linux/shell-script-get-script-location)

### 不嚴謹

```bash
#!/usr/bin/sh

echo $0 
```

但某時候會遇到問題
如./test.sh

這樣就會抓出 ./test.sh

參考:[Shell Script 取得 Script 所在目錄位置](https://www.opencli.com/linux/shell-script-get-script-location)

### 嚴謹

```bash
#!/usr/bin/sh
get_script_dir () {
     SOURCE="${BASH_SOURCE[0]}"
     # While $SOURCE is a symlink, resolve it
     while [ -h "$SOURCE" ]; do
          DIR="$( cd -P "$( dirname "$SOURCE" )" && pwd )"
          SOURCE="$( readlink "$SOURCE" )"
          # If $SOURCE was a relative symlink (so no "/" as prefix, need to resolve it relative to the symlink base directory
          [[ $SOURCE != /* ]] && SOURCE="$DIR/$SOURCE"
     done
     DIR="$( cd -P "$( dirname "$SOURCE" )" && pwd )"
     echo "$DIR"
}
echo "$(get_script_dir)"
```

參考:[Shell Script 取得 Script 所在目錄位置](https://www.opencli.com/linux/shell-script-get-script-location)

## windows

最近有時候會接觸到 BAT腳本
一樣也是遇到一樣問題
不同的是有分 C / D 曹
在不同地方處理更是麻煩
需要切換`C:`或`D:`

如:
```bat
D:
cd "D:xxx"
DIR /S /A:-D /B /O:N >list.txt
```

解決方法

```bat
@echo ===============================================
:: 切換到腳本所在的目錄
set WORK_DIR=%~dp0
echo Switch to %WORK_DIR%
cd /d %WORK_DIR%
echo %cd%
@echo ===============================================
::...加上自己 script
```
參考:[windows下bat批處理中%和%~dp0的區別_菜鳥攻城獅-CSDN博客](https://blog.csdn.net/qq981378640/article/details/52980741)

- [快閃人生: 【批次檔】%CD% 與 %~dp0](http://inpega.blogspot.com/2012/07/cd-dp0.html)
- [linux獲取當前腳本的絕對路徑 | aimuke](https://aimuke.github.io/linux/2019/04/29/how-to-get-abusolute-path/)


## 彩蛋

最近有很多 JAVA 專案是 Maven
但在找有什麼快速輸入 mvn install
後來看到[Linux 執行目錄下所有 Script](https://www.opencli.com/linux/linux-run-all-scripts-in-directory)
突然有靈感應該可以做到
有空再研究(我沒忘記的話)

