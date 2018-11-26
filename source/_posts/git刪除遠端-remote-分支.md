---
title: git刪除遠端(remote)分支和強制pull下來分支
date: 2018-11-26 20:56:00
tags: git
categories: Git
---

最近專案有新的需求
剛好用git刪除舊分支
遠端分支還是存在
沒刪掉就感覺少了什麼

<!--more-->

## git刪除遠端(remote)分支

`git push origin :cat`

> $ git push origin master:cat
意思就是要把本地的 master 分支推上去之後，在 Server 上建立 cat 分支。如果把這個指令前面的 master 拿掉：
$ git push origin :cat
就像是推了空的內容去更新線上的 cat 分支的內容，也算是變相的把該分支刪除。只是使用 Push 指令刪分支，有一點不直覺而已。

參考來源：[【狀況題】怎麼刪除遠端的分支？ - 為你自己學 Git | 高見龍](https://gitbook.tw/chapters/github/delete-remote-branch.html)

用空分支上傳覆蓋空分支，這個記法真的很方便



## 強制pull下來分支

```
I think this is the right way:

git fetch --all

Then, you have two options:

git reset --hard origin/master

OR If you are on some other branch:

git reset --hard origin/<branch_name>

```
參考來源:[version control - How do I force "git pull" to overwrite local files? - Stack Overflow](https://stackoverflow.com/questions/1125968/how-do-i-force-git-pull-to-overwrite-local-files)

`git fetch --all` 是更新 "所有" remote 底下的分支

但沒想到reset大法可以解決XD



其他重點小記
[[译] 使用 `-force` 被认为是有害的；了解 Git 的 `-force-with-lease` 命令 - 掘金](https://juejin.im/post/5985b9c56fb9a03c376de762)