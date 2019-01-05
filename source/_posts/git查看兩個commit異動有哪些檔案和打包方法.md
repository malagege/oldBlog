---
title: git查看兩個commit異動有哪些檔案和打包方法
date: 2018-11-09 21:13:17
tags: [git,git archive]
categories: Git
---

最近有同事需要我異動有哪些檔案
我找一個(方便)方法到用git看方法
在這邊小記

<!--more-->

## git diff-tree
git diff-tree
`git diff-tree --no-commit-id --name-only -r commit_id1 commit_id2`

不過這個在我公司那台怪怪的
我家電腦正常
所以才找到下面這個方法

## git diff 
`git diff --name-only  commit_id1 commit_id2`
[Making git diff --stat show full file path - Stack Overflow](https://stackoverflow.com/questions/10459374/making-git-diff-stat-show-full-file-path)


## 打包檔案方法

```
git archive --format zip -o filename.zip HEAD
```

```
git archive --format zip -o $(git log --date=short --pretty=format:"%ad" -1).zip HEAD
```

```
$ cat > ~/bin/git-zip
#!/bin/sh
git archive --format zip -o $(git log --pretty=format:"%h" -1).zip HEAD
$ chmod u+x ~/bin/git-zip
$ cd git-repository
$ git zip
```
以上範例參考如[git archive 與 log 小技巧 – Rex's blah blah blah](http://blog.nutsfactory.net/2010/02/01/git-archive-and-log/)
感覺這東西以後還是會用的到


更進階的用法:[如何讓Git匯出兩個Commit之間更新或新增的檔案 – 可達鴨村](http://lulualulu.com/%E5%A6%82%E4%BD%95%E8%AE%93git%E5%8C%AF%E5%87%BA%E5%85%A9%E5%80%8Bcommit%E4%B9%8B%E9%96%93%E6%9B%B4%E6%96%B0%E6%88%96%E6%96%B0%E5%A2%9E%E7%9A%84%E6%AA%94%E6%A1%88/)


Date: 2018-12-15

今天PM跟我要最近上傳更動幾個commit
由於我們公司上傳git方式比較不一樣
部屬程式導致有些commit有上線、有些沒有上線....

剛好之前有記錄到[如何讓Git匯出兩個Commit之間更新或新增的檔案 – 可達鴨村](http://lulualulu.com/%E5%A6%82%E4%BD%95%E8%AE%93git%E5%8C%AF%E5%87%BA%E5%85%A9%E5%80%8Bcommit%E4%B9%8B%E9%96%93%E6%9B%B4%E6%96%B0%E6%88%96%E6%96%B0%E5%A2%9E%E7%9A%84%E6%AA%94%E6%A1%88/)

裡面有
```
    echo Eport GitDiff
    set /p input1=Export Start Commit ID: 
    set commitIdStart=%input1%
    set /p input2=Export End Commit ID: 
    if [%input2%] ==[] (set commitIdEnd=HEAD) else (set commitIdEnd=%input2%)
    echo Start ID = %commitIdStart%
    echo End ID = %commitIdEnd%
    for /f "skip=1" %%x in ('wmic os get localdatetime') do if not defined DateTime set DateTime=%%x
    echo %DateTime%
    set exportFolder=%DateTime:~0,4%%DateTime:~4,2%%DateTime:~6,2%_%DateTime:~8,6%
    echo %exportFolder%
    for /f "usebackq tokens=*" %%A in (`git diff-tree -r --no-commit-id --name-only --diff-filter=ACMRT %commitIdStart% %commitIdEnd%`) do echo FA|xcopy "%%~fA" "GitExport\%exportFolder%\%%A"
    set /p DUMMY =Hit Enter To Continue....
```
這段就派上用場了

我以為這些內容都是從commit提取出來
但事實上不是這樣的
其實是透過`git diff-tree`顯示commit 改動有哪些檔案
然後她會從你現在本地端複製檔案到資料夾裡面，**不是從commit複製當時候的內容**
所以使用上也要特別注意一下head現在狀態是不是你要打包的內容

**上面敘述commit踩雷事項**
因為上次實作，需要做兩個commit`commitIdStart`、`commitIdEnd`
輸入完只有打包到兩個commit內容
但是...其實第一個commit不會包到
也就是放置兩個commit第一個commit不會算到
為什麼呢?
其實`git diff-tree`是比較兩個commit差異，所以第一個commit不會被比較到
讓我想到rebase也是一樣

**當不連續commit要怎麼辦**

PM要求不練續的commit要怎麼辦?
其實我覺得上面腳本只適合做連續commit
但今天研究了一下
其實透過上面腳本還是能做到啦

只要改腳本  時間資料夾拿掉
在多次執行腳本就可以了
雖然還是沒反杜絕手動的錯誤
但還是可以這樣用
總比人工scp跟sftp是不樣的東西複製檔案好多了吧
