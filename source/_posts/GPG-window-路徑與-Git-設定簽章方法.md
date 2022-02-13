---
title: GPG window 路徑與 Git 設定簽章方法
date: 2022-02-13 22:45:43
tags: [git,gpg]
categories: Git
---


之前[使用 GitKrakn GIT 簽章(PGP) | 程式狂想筆記](https://malagege.github.io/blog/2021/04/11/%E4%BD%BF%E7%94%A8-GitKrakn-GIT-%E7%B0%BD%E7%AB%A0-PGP/)有紀錄怎麼在GUI(Window)模式快速使用 GPG 簽章，但我最近對 GitKrakn pull幫我做 stash 感到厭煩，所以有找解決方法。[GitKraken : how to stop stash before pull ? : git](https://www.reddit.com/r/git/comments/9yr5vy/gitkraken_how_to_stop_stash_before_pull/)裡面有提到換工具或下指令，所以我只能跟 GitKrakn 說掰掰。

<!--more-->

之前家GPG簽章，原本 Git Bash commit 都會失敗，最近換成 Git-Fork 會失敗，~~還是得面對人生~~趕快找問題。簡單說Git bash 不走 window 環境上的GPG，所以會沒吃到，所以要把 Window 上的GPG(公鑰、私鑰)匯出到 Git Bash 上就可以正常 commit。


## 排除方法

gpg --list-secret-keys


路徑預設`C:/Users/User/AppData/Roaming/gnupg/pubring.kbx`
![](https://i.imgur.com/qWXsJMM.png)


匯出簽章私鑰和公鑰
![](https://i.imgur.com/VyuUFYg.png)


## 相關指令

參考:
[[GnuPG] gpg 金鑰管理 · Hello, World!](http://pre.tir.tw/008/blog/output/gnupg-gpg-jin-yao-guan-li.html)
[如何使用 GPG (GnuPG) 對 Git Commit 與 Tag 進行簽章 | The Will Will Web](https://blog.miniasp.com/post/2020/05/04/How-to-use-GPG-sign-git-commit-and-tag-object)

用 git bash 匯入 private key 

```
gpg --import xxxx.asc
```

其他要注意的 Git相關設定，我的不知道為什麼有自動調好，Git Bash 金鑰匯入後，就能直接使用
```bash
git config --global commit.gpgsign 
git config --global gpg.program 
git config --global user.signingkey

```

Git Bash、Git Fork 都能正常簽章 commit了。


## GIT 設定簽章

因為 git diff 設定壞掉，我腦根筋不對，把gitconfig 刪掉，結果一些設定都要重新用。


```
git config --global commit.gpgsign true
git config --global user.signingkey  
````

