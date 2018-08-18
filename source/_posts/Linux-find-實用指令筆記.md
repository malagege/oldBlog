---
title: Linux find 實用指令筆記
date: 2018-08-18 12:06:01
tags: [linux,find]
categories: Linux
---

最近看同事用`find`搜尋異動檔案指令
真的很方便
不用`ls -l`看了老半天
這真的太好用了
筆記筆記

<!--more-->

以下筆記是參考
{% asset_link gtwang.org_web.png "Unix/Linux 的 find 指令使用教學、技巧與範例整理 - G. T. Wang 備份圖" %}
[Unix/Linux 的 find 指令使用教學、技巧與範例整理 - G. T. Wang](https://blog.gtwang.org/linux/unix-linux-find-command-examples/)


## -mmin(實用)
find . -mmin -5

查詢現在異動時間5分鐘的檔案
這招很實用
尤其跑程式，查詢有異動那些檔案
真的很方便ˇ

## -type

`- type {xx}`找檔案類型

找檔案` -f `

找資料夾 ` -d `



## -size 

`-size 100M`剛好100MB

`-size +50M`大於50MB

`-size -50M`小於50MB


## -perm xxx

搜尋權限

`-perm 777`

## -empty
如果要找尋空檔案，可以使用 -empty 參數：
```sh
find . -type f -empty
```

找尋空目錄：

```sh
find . -type d -empty
```


參考來源:
[Unix/Linux 的 find 指令使用教學、技巧與範例整理 - G. T. Wang](https://blog.gtwang.org/linux/unix-linux-find-command-examples/)