---
title: Git刪除Rmote Branch方法
date: 2019-05-09 21:59:10
tags: [git]
categories: Git
---

之前就有類似寫過刪除 remote 分支方法
但今天想開出來紀錄
有時候刪除 remote 分支
但是我發現我本機上面可以看到刪除的分支(`git branch -a`)
要怎麼解決呢?

<!--more-->

## 本機刪除

```bash
git branch -d/-D xxx
```

## 遠端刪除(但還是會殘留在本機)

```bash
git push origin :cat
```

詳細：[git 刪除遠端(remote)分支和強制 pull 下來分支 | 程式狂想筆記](https://malagege.github.io/blog/2018/11/26/git%E5%88%AA%E9%99%A4%E9%81%A0%E7%AB%AF-remote-%E5%88%86%E6%94%AF/)

## 刪除殘留本機 remote

有時候就是不小心打指令
結果不小心`checkout`進來，它會新增到我分支
等於又要重新刪除分支

`git fetch -p` or `git fetch –all –prune`都可以解決

詳細:[關於 Git 刪除 Remote Branch - Yowko's Notes](https://blog.yowko.com/git-delete-remote-branch/)
[git 远程修剪,git 修剪,git fetch – prune 等之间的区别是什么 - 代码日志](https://codeday.me/bug/20170526/21419.html)

其他小記:
不修改 commit 內容 `--no-edit`
[Why is git prompting me for a post-pull merge commit message? - Stack Overflow](https://stackoverflow.com/questions/11744081/why-is-git-prompting-me-for-a-post-pull-merge-commit-message)

[git - Commit without setting user.email and user.name - Stack Overflow](https://stackoverflow.com/questions/22058041/commit-without-setting-user-email-and-user-name)

顯示 git commit 修程式
`git log --name-only`
[How to have git log show filenames like svn log -v - Stack Overflow](https://stackoverflow.com/questions/1230084/how-to-have-git-log-show-filenames-like-svn-log-v)
