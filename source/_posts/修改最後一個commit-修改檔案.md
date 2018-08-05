---
title: git修改最後一個commit(修改檔案)
date: 2018-07-19 21:17:08
tags: [git,amend]
categories: git
---

`git commit -amend`可修改對上一個commit檔案
git add `file`
`git commit -amend `
假如說明要變動可下
`git commit -amend 'Hello World'`


之前沒注意到git commit -amend可以對最後一個commit做修正
以為只能對commit 說明做修改
筆記筆記

不知道會不會對已經push repo有沒有影響
改天再實驗一下


[1. Commit --amend【教學3 改寫提交】 | 連猴子都能懂的Git入門指南 | 貝格樂（Backlog）](https://backlog.com/git-tutorial/tw/stepup/stepup7_1.html)