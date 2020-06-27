---
title: Discord apikey exposed on GitHub不小心上傳api key
date: 2019-05-12 13:54:04
tags:
categories: 寫程式一些事
---

今天 git push 前幾天寫的 blog 內容
收到`Discord apikey exposed on GitHub`
看了一下信，好像我上傳 apikey 到 github 上面

<!--more-->

{% asset_img 1.png 收信的內容 %}

> GitGuardian detected an API key from Discord in the following commit from \*\*\*\* pushed at 2019-05-11T17:52:24Z.
> GitGuardian, or "GG", is a "Good Guy" bot scanning in real time GitHub commits for sensitive information.
> Once you have pushed sensitive information to GitHub, this information is public and should be considered compromised. We just open sourced our GitHub repository to help developers take appropriate actions. Would love a star! :)
> Accidents happened to the best of us and will continue to happen. Sign up to our service to be notified if this happens again. We got a free tier for individual developers !

看了一下，我還真的把 discord api key 傳上去
這時候我就用 git rebase 環原去修正裡面內容

先去.deploy_git 裡面下`git rebase -i xxxx^`
要修正的 commit 請記得改`edit`再`:wq`
**修改完內容記得用`git rebase --continue`**
不是下`git commit`!!!!

[Git-Tutorials/README.md at master · twtrubiks/Git-Tutorials · GitHub](https://github.com/twtrubiks/Git-Tutorials/blob/master/README.md)
平常沒什麼機會用到
今天碰到了 XD

不過 hexo deploy 的網頁檔沒有抓到
他是抓到我上傳 markdown 檔案
看來不會對大檔案做簡查
 