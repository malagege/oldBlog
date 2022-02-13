---
title: 'ssh 使用 ControlMaster 做第二連線自動登入(xshell,putty)'
date: 2022-02-13 23:15:59
tags: [ssh,sshconfig]
categories: Linux
---

有時候我們連一台主機。不可能只開一個 ssh 連線。每次連線都要重新輸入帳號密碼，真的也很花時間。無形之中又多做一次驗證，最近看到這個方法，簡單研究一下，希望能幫我開發上縮短幾次幾秒鐘XD。

<!--more-->

## ssh(ControlMaster)

![](https://i.imgur.com/hJcHWNF.png)


```
ControlMaster   auto
ControlPath     /tmp/ssh-%r@%h:%p
```

```
ssh -vvv master
```
可以看到 log 沒有做金鑰驗證。
![](https://i.imgur.com/yafSD1n.png)

使用密碼方式也能用這個方法喔!!!

參考:[增進 SSH 使用效率 - ssh_config · 完全用 GNU/Linux 工作](https://chusiang.gitbooks.io/working-on-gnu-linux/content/20.ssh_config.html)

更多設定可以看[SSH Config 那些你所知道和不知道的事 - 台部落](https://www.twblogs.net/a/5c4b594ebd9eee6e7e06d6c9)。

## putty

登入前設定下面選項
(Putty Configuration -> Connection-> SSH -> `Share SSH connections if possible` 打勾)

![](https://i.imgur.com/FPovctc.png)

第二視窗一樣要做這個選項，登入就不需要密碼了。

![](https://i.imgur.com/BMTC3ht.png)


## xshell

### 方法1

```
xshell克隆一個新的終端:shift + alt + t
```

參考連結：[xshell克隆會話_Marion的博客-CSDN博客_xshell 複製會話](https://blog.csdn.net/marion158/article/details/90690394)


### 方法2

```
Windows篇

利用xshell和SecureCRT session clone的思路，以xshell為例。對跳板機的會話屬性做如下設置：

1. 在會話屬性--連接--用戶身份驗證中的方法，選擇keyboard Interactive，輸入用戶名和密碼。

2. 這樣登陸時，密碼都可省略，直接輸入token即可。

3. 在已經登陸的session上，右鍵點擊tab頁，選擇複製ssh渠道，就可以免token無限複製啦。
```

參考:[登陸跳板機每天只輸入一次token的方法——ssh clone session - Solon Tan - 博客園](https://www.cnblogs.com/solontan/p/4048847.html)


![](https://i.imgur.com/N73h99V.png)

這邊看起來要用 私鑰登入



## ssh autocomplete

```shell=
_complete_ssh_hosts ()
{
        COMPREPLY=()
        cur="${COMP_WORDS[COMP_CWORD]}"
        comp_ssh_hosts=`cat ~/.ssh/known_hosts | \
                        cut -f 1 -d ' ' | \
                        sed -e s/,.*//g | \
                        grep -v ^# | \
                        uniq | \
                        grep -v "\[" ;
                cat ~/.ssh/config | \
                        grep "^Host " | \
                        awk '{print $2}'
                `
        COMPREPLY=( $(compgen -W "${comp_ssh_hosts}" -- $cur))
        return 0
}
complete -F _complete_ssh_hosts ssh
```

上面加在 `.profile` 就可以開心始用。:D

參考:[Add auto complete to your ssh, put into your .bash_profile](https://gist.github.com/aliang/1024466)