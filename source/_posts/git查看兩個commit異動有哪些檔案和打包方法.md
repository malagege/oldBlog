---
title: git查看兩個commit異動有哪些檔案和打包方法
date: 2018-11-09 21:13:17
tags: [git]
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