---
title: Linux 指令搜尋多個檔案內容
date: 2019-09-23 21:00:57
tags: [grep,find]
categories: 實用工具
---

最近需要做搜尋多個檔案
但又不想把每一個檔案都抓下來
有網路找幾個方法，做個筆記


<!--more-->

## Linux 指令方法

```bash
find "path name" -name "file name" -exec grep -H "search content" {} \;
```
[Linux搜尋資料夾下的檔案內文：find+grep指令產生器 / Search the Content of Files on Linux: find + grep Command Builder - 布丁布丁吃什麼？](http://blog.pulipuli.info/2017/03/linuxfindgrep-search-content-of-files.html)
[Falldog的程式戰場: [Linux] 搜尋資料夾底下 檔案內部文字](http://falldog7.blogspot.com/2008/01/linux.html)

這個指令不是很快(不知道是不是真的資料太多啦XD)
主機指令不想安裝太複雜的程式
所以我就沒試了

## 其他工具

[4ndrew/filesearcher: File Search experiments](https://github.com/4ndrew/filesearcher)
[danielgatis/search_in_files: This is a tool for search text in files](https://github.com/danielgatis/search_in_files)


快速找檔案，但沒有找內容
[vishaltelangre/ff: Find files (ff) by name, fast!](https://github.com/vishaltelangre/ff)
fzf 也能做到
[Make fzf installable by using `go get` · Issue #801 · junegunn/fzf](https://github.com/junegunn/fzf/issues/801)