---
title: sshconfig 設定小記
date: 2022-02-13 23:15:16
tags: [ssh,sshconfig]
categories: Linux
---

一般我們在 ssh 連線都會用 putty，但是在多台機器管理上不是很方便，所以回換成 xshell 相關工具做連線查詢，最近在做`Bastion Host`，發現ssh 也可以做到簡單管理設定，這邊就做個筆記。

首先，可以在 `.ssh/config` 做多個 ssh 連線設定，裡面設定值不分大小寫，可看官網文件。[SSH config file syntax and how-tos for configuring the OpenSSH client](https://www.ssh.com/academy/ssh/config)

> The possible keywords and their meanings are as follows (note that keywords are case-insensitive and arguments are case-sensitive):

<!--more-->

## 常用設定

```sshconfig
# - master
Host            master                # 代號
Hostname        192.168.11.24        # IP or Domain name
Port            2222                # 指定埠口
User            jonny                # 使用者名稱
identityfile    ~/.ssh/id_rsa_24    # 指定金鑰

# - slave
Host            slave                # 代號
Hostname        192.168.11.25        # IP or Domain name
Port            2223                # 指定埠口
User            jonny                # 使用者名稱
identityfile    ~/.ssh/id_rsa_25    # 指定金鑰
```

參考:[增進 SSH 使用效率 - ssh_config · 完全用 GNU/Linux 工作](https://chusiang.gitbooks.io/working-on-gnu-linux/content/20.ssh_config.html)

有看到有人會做編排，在排版上面會比較好處理。

```sshconfig
Host            slave                # 代號
    Hostname        192.168.11.25        # IP or Domain name
    Port            2223                # 指定埠口
    User            jonny                # 使用者名稱
    identityfile    ~/.ssh/id_rsa_25    # 指定金鑰
```
### 詳細設定

有做過ControlMaster 設定，可以參考  這篇。

官網其他設定可以看[SSH config file syntax and how-tos for configuring the OpenSSH client](https://www.ssh.com/academy/ssh/config)

## 其他應用

有看到[How to setup SSH config ：使用 SSH 設定檔簡化指令與連線網址 | by awonwon | 浦島太郎的水族缸 | Medium](https://medium.com/%E6%B5%A6%E5%B3%B6%E5%A4%AA%E9%83%8E%E7%9A%84%E6%B0%B4%E6%97%8F%E7%BC%B8/how-to-setup-ssh-config-%E4%BD%BF%E7%94%A8-ssh-%E8%A8%AD%E5%AE%9A%E6%AA%94-74ad46f99818)，這篇可以用設定 ssh 連線，在做 git 不需要打那麼長。

剩下有看到什麼在補這邊

## 金鑰管理

我一直在想為什麼 ssh 連線會自動吃`.ssh/id_rsa`檔案，但是雖然可以產生別的 key，但我最近在想是不是使用 Linux帳號去管理這件事會比較好?因為切換帳號可以直接ssh 連線，然後正常安裝程式也會建議使用該程式帳號去做建置，這可能是我目前想到比較好作法。

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


相關連結
[教你高效使用SSH 的16 个技巧_shinepaopao-CSDN博客](https://blog.csdn.net/iteye_20364/article/details/82537051)