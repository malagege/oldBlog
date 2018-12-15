---
title: git push簡單自動部屬程式方法
date: 2018-12-15 19:25:11
tags: [git]
categories: Git
---

其實最近想git有甚麼好方法做部屬動作
優蒐集網路一些方法
都是`delete all file`再刪除....
但感覺這種作法，會讓服務中斷
有想到用`git pull`更新方法
但Server Web目錄會有git
雖然有想到用git方法
不過最終有找到不錯的做法

<!--more-->

## Git 如何針對其他路徑下的 Repository 執行指令

最近剛好看到這篇
`git -–git-dir={專案路徑}.git\ -–work-tree={專案路徑} {git 指令}`
看到這個舊感覺能用在git簡單部屬上面

所以就稍微在我Linux電腦主機上面玩了一下

簡單原理`git-dir`運用在git 資料夾，這邊範例是`.git`結尾
我一開始以為一定要用`bare`
但後來發現，除了可以用`bare`當git
還能用一般`project_dir/.git/`

work-tree 就是要對應其路徑


看到這個就能感覺輕鬆部屬
.git/hooks/post-receive
```
# !/bin/sh

# checkout HEAD
git --get-dir=xxxxx/.git/ --work-tree=xxxx(部屬位置) checkout -f

```
```bash
chmod +x .git/hooks/post-receive
```
[How to deploy projects with Git - Buddy](https://buddy.works/blog/how-deploy-projects-with-git)
雖然感覺也是能用`git reset --hard head`
不過網路上用這個比較多
也有看到做之前`git checkout -f`做`git stash`
雖然這篇是透過push git repo去做更新

但我覺得不應該讓bare有兩個
改天push錯就GG  XD

之前也有研究[用caddy-git使用webhook初體驗 | 程式狂想筆記](https://malagege.github.io/blog/2017/11/26/logdown/2017-11-26-3865744/)
感覺可以運用這個上面
`then        command [args...]`應該就可以做`git --get-dir=xxxxx/.git/ --work-tree=xxxx(部屬位置) checkout -f`

目前在想
```
dev_git --push-->git reop --> webhook --> caddy webhook --> git deploy
```

當然這邊只能做不需要編譯的部屬
例如PHP應該能這樣做
說不定哪天就有機會可以用到


其他參考來源:

* [Git 面試題 - Git 教學 | 高見龍](https://gitbook.tw/interview)
* [abiosoft/caddy-git: git middleware for Caddy](https://github.com/abiosoft/caddy-git)
* [simple-php-git-deploy,使用PHP和Git自動部署代碼,下載simple-php-git-deploy的源碼_GitHub_幫酷](https://hant.helplib.com/GitHub/article_94904)
* [从Git部署一个PHP项目到没有安装Git的服务器 - 代码日志](https://codeday.me/bug/20181205/437453.html)
* [安全的代码部署 · php笔记 · 看云](https://www.kancloud.cn/xiak/php-node/581828)
* [SegmentFault](https://mp.weixin.qq.com/s/qZGVdNtCJOycR_sye9az6w?)
* [git 服务器 搭建/自动部署/远程连接 | Caoxl's Blog](http://blog.caoxl.com/2018/06/14/Git-Server-Deploy/)
* [git hook实践心得 - 腾讯Web前端 IMWeb 团队社区 | blog | 团队博客](http://imweb.io/topic/5b13aa38d4c96b9b1b4c4e9d)
* [git remote add with other SSH port - Stack Overflow](https://stackoverflow.com/questions/3596260/git-remote-add-with-other-ssh-port)
* [用caddy-git使用webhook初體驗 | 程式狂想筆記](https://malagege.github.io/blog/2017/11/26/logdown/2017-11-26-3865744/)
* [abiosoft/caddy-git: git middleware for Caddy](https://github.com/abiosoft/caddy-git)