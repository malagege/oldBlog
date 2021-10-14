---
title: AIX(ksh) 操作小記
date: 2021-10-09 23:33:54
tags: [aix,ksh]
categories: Aix
---

最近做某個專案主機是用AIX，裡面的 command line 跟以往的 Bash 不一樣，沒有 autocompute 功能，也沒有 bash 上下找 History 功能。有時按方向鍵反而不是我想要的結果，搜尋一下，才發現使用操作上跟 vi 一樣。

簡單說一下 AIX，是一個 UNIX 系統，功能比 Linux 少很多功能樣子，使用上跟 Bash 差很多，沒 watch, top ...等等功能，真的不習慣。

<!--more-->


### 模式切換到 vi

按裝預設可能不是設定 vi，可以設定到 .profile

```bash=
set -o vi
```

[How to invoke ESC+K](https://www.unix.com/unix-for-dummies-questions-and-answers/122586-how-invoke-esc-k.html)

### anything is vi 

1. command line 操做方向都是 hjkl(左下上右)。

2. 有查到網路上看查歷史使用`ESC+K`(上)+`ESC+J`(下)。

3. 正常方向鍵都不能使用，跟第一點`hjkl(左下上右)`操做是一樣的。

4. 插入打字是`a,i,r`(跟 vi 打字一模一樣)。

[vi/vim delete commands and examples | alvinalexander.com](https://alvinalexander.com/linux/vi-vim-delete-line-commands-to-end/)

### ksh 版本

```
WIN-A5LTRLM4VQ5:/mnt/c/Users/Administrator$ ksh

Command 'ksh' not found, but can be installed with:

sudo apt install ksh    # version 2020.0.0-5, or
sudo apt install ksh93  # version 93u+20120801-7
sudo apt install mksh   # version 58-1
```


[How To: Find Out KSH ( Korn Shell ) Version on Linux or Unix-like system - nixCraft](https://www.cyberciti.biz/faq/unix-linux-appleosx-get-ksh-version-check-ksh-version/)

aix 好像是用`ksh93`。


### auto completion

找了很多篇，沒特別說明，剛好翻到這篇[Tab Completion - Shell Scripting: Expert Recipes for Linux, Bash, and More [Book]](https://www.oreilly.com/library/view/shell-scripting-expert/9781118166321/c11-anchor-12.xhtml)
，原來是這樣用的orz。

```bash=
ksh$ ca<TAB><TAB>
 1) /usr/bin/cancel
 2) /usr/bin/callgrind_annotate
 3) /usr/bin/cameratopam
 4) /usr/bin/callgrind_control
 5) /usr/bin/cancel.cups
 6) /usr/bin/cal
 7) /usr/bin/captoinfo
 8) /usr/bin/catchsegv
 9) /usr/bin/card
10) /usr/sbin/cacertdir_rehash
11) /usr/sbin/callback
12) /bin/cat
6<TAB>
ksh$ /usr/bin/cal
     March 2011
Su Mo Tu We Th Fr Sa
       1  2  3  4  5
 6  7  8  9 10 11 12
13 14 15 16 17 18 19
20 21 22 23 24 25 26
27 28 29 30 31
$


ksh$ cat /etc/host<TAB><TAB>
1) host.conf
2) hosts
3) hosts.allow
4) hosts.deny
4<TAB>
ksh$ cat /etc/hosts.deny
```

https://ngelinux.com/aix-korn-shell-how-to-set-tab-auto-completion-and-hostname-present-working-directory-path-in-the-terminalps1-variable/

### 公司使用 ksh tab 沒有反應

使用 ksh93 就能用，位置在`/usr/bin/ksh93`

https://ngelinux.com/aix-korn-shell-how-to-set-tab-auto-completion-and-hostname-present-working-directory-path-in-the-terminalps1-variable/


## 實用指令

[AIX中的进程管理 - 春风亭牛二哥 - 博客园](https://www.cnblogs.com/liuxing0007/p/11009901.html)[備份圖](https://i.imgur.com/1TfZgZ6.png)

[Canceling a background job - IBM Documentation](https://www.ibm.com/docs/en/zos/2.1.0?topic=job-canceling-background)

less -> cat, view

## /dev/null

[AIX 根目录满了，发现/DEV/null 2>&1文件很大，如何删除-CSDN社区](https://bbs.csdn.net/topics/391867270)



## aix 其他指令

[超詳細的AIX系統命令總結（慎重執行） - 每日頭條](https://kknews.cc/zh-tw/code/2yboa9r.html)

### 參考來源

[Linux-shell-完全詳解命令_Linux Linux-shell-完全詳解 命令用法详解：](https://man.linuxde.net/linux-shell-%E5%AE%8C%E5%85%A8%E8%A9%B3%E8%A7%A3)


[aix auto-completion, command history function (finishing)(Others-Community)](https://titanwolf.org/Network/Articles/Article?AID=c5d44a08-ed0a-4730-8ef5-c2071a6ef91a#gsc.tab=0)

[set -o vi AIX下shell - 周人假的 - 博客园](https://www.cnblogs.com/zhouhbing/p/4275699.html)


[XXOOXp: AIX版的Linux watch指令...(監控指令的輸出狀態)](http://xxooxp.blogspot.com/2011/09/aixlinux-watch.html)

[AIX ksh使用小技巧_wujay-CSDN博客](https://blog.csdn.net/iteye_20080/article/details/82546705)