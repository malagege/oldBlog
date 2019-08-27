---
title: csv2xlsx CSV 轉 Excel 方法
date: 2019-08-26 23:04:58
tags: [csv,xlsx]
categories: 實用工具
---

最近查詢報表
用 SQL 查詢 MySQL 資料，產生出 csv 檔案
但是在 Excel 直接打開會是亂碼，因為 DB 存放資料是 UTF-8
這個很簡單，使用 Notepad++ 或 vscode 改編碼就可以解決這個問題
改 Big5 裡面的韓語、日語就會變亂碼
其實可以手動到 Excel 匯入
我覺得需要有快速執行工具，就找找現成的 :P

<!--more-->

## tealeg/csv2xlsx

找了這麼多版，終於找到一款可以正常使用。
[tealeg/csv2xlsx: A simple program to convert CSV files into XLSX files.](https://github.com/tealeg/csv2xlsx)

### 安裝方法

注意前面沒有`http`
```bash
go get github.com/tealeg/csv2xlsx
```

### 使用方法

```bash
csv2xlsx -f=xxxx.csv -o=test.xlsx -d=,
```

### 移除 golang 程式方法

```bash
# 看看會做哪些動作，程式安裝在哪裡
go clean -i -n github.com/google/acme 
#移除程式
go clean -i github.com/google/acme 
```

參考:[Golang get 抓取的套件如何移除 | Tsung's Blog](https://blog.longwin.com.tw/2017/03/golang-get-%E6%8A%93%E5%8F%96%E7%9A%84%E5%A5%97%E4%BB%B6%E5%A6%82%E4%BD%95%E7%A7%BB%E9%99%A4/)


### 注意事項

需使用 UTF-8 csv
產生 Excel 為 utf8
big5 會亂碼


## 其他的 csv2xlsx

### bash 指令方法

SSConvert 

unoconv 

但好像都是 Linux ，所以我就沒有嘗試

[bash - convert CSV to XLS file on linux - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/158254/convert-csv-to-xls-file-on-linux)

### 其他

* [mentax/csv2xlsx: Fast and simple opensource command line tool to convert CSV do XLSX](https://github.com/mentax/csv2xlsx)
* [Armin / csv2xlsx · GitLab](https://gitlab.com/DerLinkshaender/csv2xlsx)
* [csv2xlsx · PyPI](https://pypi.org/project/csv2xlsx/)
* [kyoh86/csv2xlsx](https://github.com/kyoh86/csv2xlsx)
* [truesocialmetrics/service-file-format-convertor-csv2xlsx: convert files from csv to excel](https://github.com/truesocialmetrics/service-file-format-convertor-csv2xlsx)
* [Golang get 抓取的套件如何移除 | Tsung's Blog](https://blog.longwin.com.tw/2017/03/golang-get-%E6%8A%93%E5%8F%96%E7%9A%84%E5%A5%97%E4%BB%B6%E5%A6%82%E4%BD%95%E7%A7%BB%E9%99%A4/)