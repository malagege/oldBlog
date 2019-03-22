---
title: git從某一個commit還原某一個檔案和git log搜尋檔案方法
date: 2019-03-21 19:50:41
tags: [git]
categories: Git
---

最近修改程式，想還原之前某一隻檔案的時候
下意識想做`git reset`
但是突然想到我只想還原一個檔案
雖然我有 vscode 可以用 gitlens 去看之前檔案全選出來
但還是覺得看看有沒有找檔案的方式

<!--more-->

## git 從某一個 commit 還原某一個檔案

最後找到用`git checkout`方式還原
等等，我記得之前運用都是當下 commit 還原一個檔案
沒想到她竟然還有指定 commit 功能?!!

這邊可能很多人會忘記`git checkout`跟`git reset`還原差異
可以看裡面的圖，幫助回憶[git reset 和 git reflog 小記 | 程式狂想筆記](https://malagege.github.io/blog/2018/11/09/git-reset-%E5%92%8Cgit-reflog%E5%B0%8F%E8%A8%98/)

### one file recover

```bash
## 還原指定一隻檔案
## head -- 可以省略(前提不要跟自己的branch撞到名子就好，通常撞到機率低)
git checkout head -- application/controllers/xxxx/xxxx.php
git checkout b01* -- application/controllers/line/LibraryPlaylist.php
git checkout head -- application/controllers/line/LibraryPlaylist.php
```

### all file recover

```
git reset HEAD
```

##

[Git 還原單一檔案 | 愛吃東西的 RD](http://www.andrewchen.tw/2017/02/11/20170211_NOTE_git%E9%82%84%E5%8E%9F%E5%96%AE%E4%B8%80%E6%AA%94%E6%A1%88/)

## git log 搜尋檔案方法

常常使用 gitlens 功能
他有一個很神奇功能
可以看當下檔案存在那些 commit
看當下行數有存在那些 commit
今天好奇查了一下指令要怎麼下

### file history

```bash
git log '*/xxxx.php'
```

### line history

```bash
git log -L 103,103:application/xxxx/xxxx.php
```

### gitk log

```bash
gitk '*/Trackinfo.php'
#這邊L後面數字要連在一起
gitk -L103,103:application/models/line/api/Mod_libraryPlaylist.php
```
