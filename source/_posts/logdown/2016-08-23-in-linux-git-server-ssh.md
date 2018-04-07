---
layout: post
title: '在Linux架Git Server(ssh)'
date: 2016-08-23 12:14
comments: true
categories: Git
tags: [git]
---
最近公司接了一個案子，我被分配到架設Git Server工作
架設成功，就在這裡做簡單的筆記。
由於網路教的都大同小異，只需理解為什麼這樣做
用自己的需求，基本上實作應該都能做出來

<!--more-->
首先，先建立git帳號

```
adduser git
```

然後，Git Server說穿了就是用ssh方式做存取
所以必需用RSA金鑰登入(不需打密碼這種)，多虧了這次我終於了解ssh金鑰登入方式
```client|sh
ssh-keygen -t rsa
```
家目錄就會產生了.ssh資料夾
裡面有id_rsa.pub id_rsa兩個檔案(前者是公開金鑰[給Server]、後者是私密金鑰[Clinet自己保留])

在Server的git家目錄把.ssh\authorized_keys加入公開金鑰
或 上傳公開金鑰直接用指令加入
```
cat /tmp/id_rsa.john.pub >> ~/.ssh/authorized_keys
```
**注意：.ssh資料夾必需要為700，authorized_keys必需為600或root owner**

此時可以進行ssh登入嘗式，能登入git帳號成功，就差不多了。


在家目錄建立
```
mkdir xxx.git
cd xxx.git
git --bare init
```

好了，接下來就做commit上去試試
```
$ cd myproject
$ git init
$ git add .
$ git commit -m 'initial commit'
$ git remote add origin git@xxx.xxx.xxx.xx:xxx.git
$ git push origin master
```

完畢。

什麼，你說還沒完XD，由於安全性的問題
git應該不該登入帳號
```
sudo vim /etc/passwd
git:x:1000:1000::/home/git:/usr/bin/git-shell
```

好了這樣就完成了。


你說還有https、Gitosis和GitLab
各位勇者就靠你們研究嘍XD

在這次實作，我發現可以用scp指令傳檔案上去
pietty 0.3可以傳(拖移檔案)，以後傳檔案不用在開ftp了(淚)

參考來源：
伺服器上的 Git - 架設伺服器
https://git-scm.com/book/zh-tw/v1/%E4%BC%BA%E6%9C%8D%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E6%9E%B6%E8%A8%AD%E4%BC%BA%E6%9C%8D%E5%99%A8
在 Linux 中架設 Git 伺服器教學（使用 SSH 加密傳輸）
https://blog.gtwang.org/linux/linux-git-server-using-ssh/
給windows使用者的簡易Git環境架設 (linux架設)
http://eric0806.blogspot.tw/2014/05/ubuntu-git-server-for-small-team-who-use-windows-os.html
Ubuntu架設GitServer
http://samchu.logdown.com/posts/777348-set-up-ubuntu-gitserver
CentOS架設GitServer
http://samchu.logdown.com/posts/778052-set-up-centos-gitserver
Fedora, CentOS ssh 登入到遠端 Server 不用密碼
http://linux988.pixnet.net/blog/post/25926301-fedora,-centos-ssh-%E7%99%BB%E5%85%A5%E5%88%B0%E9%81%A0%E7%AB%AF-server-%E4%B8%8D%E7%94%A8%E5%AF%86%E7%A2%BC