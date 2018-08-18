---
title: git log簡單用法筆記
date: 2018-08-17 23:47:07
tags: [git,log]
categories: Git
---

最近學會使用`git log`
但怕很久沒用就會忘了
簡單記錄一下

<!--more-->

**Git branch(分支)是一個pointer概念**
通常習慣用`gitk`看分支圖片
再不會可以用`TortoiseGit`和`sourcetree`
在下兩個都不排斥
但是sourcetree安裝時候要登入帳號密碼
所以我沒有在公司用了

但今天不是要講gui界面多好用
你會看`git log  --graph --oneline`
就可以省略看GUI界面


平常在分之底下看`git log`，一開始覺得奇怪怎麼不顯示全部分支
**事後懂了git branch是一個pointer概念**
覺得看全部真的沒有意義(應該正常需求不會看到)
全部看到會覺得太亂
通常在這條分支看過去紀錄，反正merge過後`git log`都看到
真的沒必要多個`--all`

PS:看圖`--graph`

`--oneline`顯示一行，英文就是`one line`和在一起

`-p -(n)`秀diff 程式碼
>$ git log -p -2
最常用的選項之一為 -p，用來顯示每個更新之間差別的內容。 另外還可以加上 -2 參數，限制為只輸出最後兩個更新。

通常也不用死記
`git bash`tab 可以自動autocomplete
(工程師沒辦法記太多東西>.<)

剛開始看不是很懂`git log`全部分支
但自從懂git branch是pointer概念
就開竅了XDD

參考來源:
[Git - 檢視提交的歷史記錄](https://git-scm.com/book/zh-tw/v1/Git-%E5%9F%BA%E7%A4%8E-%E6%AA%A2%E8%A6%96%E6%8F%90%E4%BA%A4%E7%9A%84%E6%AD%B7%E5%8F%B2%E8%A8%98%E9%8C%84)