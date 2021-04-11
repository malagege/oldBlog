---
title: 使用 GitKrakn GIT 簽章(PGP)
date: 2021-04-11 23:43:57
tags: [git,pgp]
categories: Git
---

最近因緣際會下用到 PGP
因為我們公司有用 gitlab 
之前就想研究 Git 簽章怎麼用
但一直都沒有去嘗試
最近碰到 PGP 加密
竟然碰到相關問題，馬上使用看看

Git mail 使用者名稱都可以自己改上傳
使用簽章後，能證明GIT commit 是誰做的

本篇是以方便使用 GUI 介面操作(Gitkrakn)
並非以指令方式去做
相關指令推薦爬相關專業文章


<!--more-->

## 相關 command 教學

[Commit Signing with gpg - GitKraken Documentation](https://support.gitkraken.com/git-workflows-and-extensions/commit-signing-with-gpg/)


[如何使用 GPG (GnuPG) 對 Git Commit 與 Tag 進行簽章 | The Will Will Web](https://blog.miniasp.com/post/2020/05/04/How-to-use-GPG-sign-git-commit-and-tag-object)


[利用 GPG 簽署 git commit | Puck's Blog](https://blog.puckwang.com/post/2019/sign_git_commit_with_gpg/)

## 簡單教學

其實還滿簡單

在 window 安裝 gpg4win

設定 gitkrakn 那邊按 Generate (基本上指令可以打`GPG`就不需要設定)
![](https://i.imgur.com/3HEBH0n.png)

GitLab 設定把公鑰用到上面就可以驗證過了，但注意 git mail 名稱要跟 gitlab mail 一樣
![](https://i.imgur.com/g8oCWE2.png)

sign commits by default 記得要打勾
雖然我還沒找到手動 commit 簽章選項
有找到再回來寫

[New added PGP key is unverified (#37572) · Issues · GitLab.org / GitLab FOSS · GitLab](https://gitlab.com/gitlab-org/gitlab-foss/-/issues/37572)

這邊注意簽章信箱要跟gitlab 一樣才不會有問題
