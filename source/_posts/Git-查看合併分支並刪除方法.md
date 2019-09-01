---
title: Git 查看合併分支並刪除方法
date: 2019-08-27 21:39:55
tags: [git]
categories: Git
---

最近常常看哪些 branch 有做合併動作
我都使用`gitk --all` 看合併
但我覺得應該有很方便指令看的方法

<!--more-->

官方文件有詳細說明
[Git - 分支的管理](https://git-scm.com/book/zh-tw/v1/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E7%AE%A1%E7%90%86)

## git branch --merged

show 出來已經 merged 的分支


## git branch --no-merged

以此類推，list出沒合併的分支

## 其他備註

之前有做過[Git刪除Rmote Branch方法 | 程式狂想筆記](https://malagege.github.io/blog/2019/05/09/Git%E5%88%AA%E9%99%A4Rmote-Branch%E6%96%B9%E6%B3%95/)
**看來local 端的 git branch 要自己 `git branch -d xxx`**
沒辦法自動 delete 掉

git fetch -p or git fetch –all –prune 是清除本地的remote 暫存分支


相關連結
[[git]如何快速清理已經合並(merged)的分支(branch) - local及remote @ Alan Tsai 的學習筆記｜An Asp .Net Mvc Web Developer Blog](https://blog.alantsai.net/posts/2019/04/faq-git-how-to-delete-merged-branches-from-remote-and-local)