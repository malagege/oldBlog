---
title: 使用 Bastion Host 方式ssh方式登入主機
date: 2022-02-13 23:09:21
tags: [ssh, bastion, sshconfig]
categories: Linux
---

最近參照[主機操作文件](https://github.com/cloud-native-taiwan/Infra-Labs-Docs/blob/main/user-guide/openstack.md)建置 Bastion Host，常常 ssh 登入主機，覺得操作上不是很方便，所以後來改用 Xshell,Pietty(putty) 連線 Linux 主機，發現 ssh 也滿強的。

<!--more-->

## 你可能需要懂的東西

[[教學] 產生SSH Key並且透過KEY進行免密碼登入 | 辛比誌](https://xenby.com/b/220-%E6%95%99%E5%AD%B8-%E7%94%A2%E7%94%9Fssh-key%E4%B8%A6%E4%B8%94%E9%80%8F%E9%81%8Ekey%E9%80%B2%E8%A1%8C%E5%85%8D%E5%AF%86%E7%A2%BC%E7%99%BB%E5%85%A5)

### sshconfig

[SSH config file syntax and how-tos for configuring the OpenSSH client](https://www.ssh.com/academy/ssh/config)

### ssh-agent

[ssh转发代理：ssh-agent用法详解 - 骏马金龙 - 博客园](https://www.cnblogs.com/f-ck-need-u/p/10484531.html)



## 方式

### ssh-agent forward(不推薦)

這個方法有風險，所以不推薦使用。可以參考這篇[淺談 SSH agent forwarding 和 proxy command 的安全風險與應用 « Nic Lin's Blog](https://blog.niclin.tw/2019/08/03/ssh-agent-forwarding-and-proxy-command/)，有詳細說明。

### ProxyCommand

這個在網站看到有很多不同寫法。我在嘗試不知道要用哪一個...，也不知道他們之間的差異，不過後來看到這篇[ssh命令之ProxyCommand選項 | dslztx](https://dslztx.github.io/blog/2017/05/19/ssh%E5%91%BD%E4%BB%A4%E4%B9%8BProxyCommand%E9%80%89%E9%A1%B9/) [備份圖](https://i.imgur.com/AvntHHA.png)，裡面有詳細講到差異，這邊有興趣的人可以看一下。

```sshconfig
Host            PI1
User            pi
Hostname        192.168.1.201


Host            PI2
User            pi
Hostname        192.168.1.202
ProxyCommand    ssh -W %h:%p PI1
```

這邊要注意要改 ProxyCommand 的`PI1`，無腦貼可能不能跑。所以還是推薦使用 ProxyJump ，還算滿方便的。

### ProxyJump


#### sshconfig

使用上非常簡單，把 ssh host 加在 `ProxyJump` 就完事了。


```sshconfig

Host            PI1
User            pi
Hostname        192.168.1.201


Host            PI2
User            pi
Hostname        192.168.1.202
ProxyJump       PI1                                
```

#### 一般指令

```bash=
ssh -J pi@192.168.1.201   pi@192.168.1.202   
```

後來看到可以做多個跳板方式，使用`,`分隔就能做到。

```
ssh  -J pi@192.168.1.201,pi@192.168.1.202   pi@192.168.1.203    
```

參考: [使用 ssh Proxyjump | Nyo's Study Book](https://nyogjtrc.github.io/posts/2020/03/ssh-proxyjump/)


#### scp

scp
```
scp -o 'ProxyJump jump.host' myfile.txt dist.host:/my/dir
```

當然有設定 ssh_config 也能直接做 scp 動作。

```bash=
scp a.sh PI2:~    
```


參考:[SSH to remote hosts though a proxy or bastion with ProxyJump | Enable Sysadmin](https://www.redhat.com/sysadmin/ssh-proxy-bastion-proxyjump)

之前用winscp做 ssh tunnel 只能跳一層，但這個應該可以無限跳。可以參考[ssh port forward連MySQL方法(SSH Tunnel) | 程式狂想筆記](https://malagege.github.io/blog/2018/12/13/ssh-port-forward%E9%80%A3MySQL%E6%96%B9%E6%B3%95/)

## 相關文章

[Day 12 - 使用 CDK 部署 Bastion Host 防禦主機！ - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10244418)
[安全地利用 Bastion Host 跳轉. Create secure SSH connection via… | by HsuCheng Tseng | Medium](https://medium.com/@puresmash/%E5%AE%89%E5%85%A8%E5%9C%B0%E5%88%A9%E7%94%A8-bastion-host-%E8%B7%B3%E8%BD%89-5f0529301362)
[認識 Bastion Host 部署管理機 - Yi Yang's Blog](https://www.yylin.io/2019/01/19/bastion-host/)
[淺談 SSH agent forwarding 和 proxy command 的安全風險與應用 « Nic Lin's Blog](https://blog.niclin.tw/2019/08/03/ssh-agent-forwarding-and-proxy-command/)

## vagrant 測試 ProxyJump 功能

本來想用網路上寫好的 vm 測試，但後來發現不用，想側功能直接 JUMP 自己連自己就好啦XDD。VM測試我目前還沒有想到怎麼隔離內網連電腦問題，可能專業虛擬環境才有辦法。

```
ssh  -J pi@192.168.1.201,pi@192.168.1.201   pi@192.168.1.201
```

[jcoetsie/bastion_exercise: Learn how a bastion works using vagrant and virtualbox](https://github.com/jcoetsie/bastion_exercise)
[vverecke/proxycommand_demo: Automatically sets up a development environment using Vagrant and Ansible to demonstrate the usage of bastion hosts.](https://github.com/vverecke/proxycommand_demo)

![](https://i.imgur.com/xMncH4z.png)
這邊選擇可橋接網路


## 為什麼這個安全與我的心得

> 如果不走 VPN，就需要開通從外網走 22 port 進目標 AWS EC2 主機的連線。就算有白名單的限制，這樣寬鬆的設定並不算安全，在此我們可以利用俗稱 Bastion Host 的中繼主機當跳板，如此甚至可以只在需要時打開跳板主機，進而完全隔離外網。這篇文章將介紹如何安全地穿過跳板連上目標主機，以及可能發生的問題。

參考: [安全地利用 Bastion Host 跳轉. Create secure SSH connection via… | by HsuCheng Tseng | Medium](https://medium.com/@puresmash/%E5%AE%89%E5%85%A8%E5%9C%B0%E5%88%A9%E7%94%A8-bastion-host-%E8%B7%B3%E8%BD%89-5f0529301362)

上面文章有簡單說明，我自己也有思考為什麼這個會比較安全，一般公司都會設好VPN，但連進去網路可能就跟公司同網段，在這樣情況下駭進去第一台電腦就可以針對該網段做動作(scan ip 或 抓該電腦資料)。如果我使用 Bastion Host 作法，就算外層被駭進去，把內部網段與 Bastion Host 隔離開，這樣安全性也會比較高。

使用 Bastion Host 這個方式可以在跳板主機不惠存到連線方式，這個還滿特別的。我目前三份工作，公司沒有使用到 Bastion Host 這種方法，但是有跳板主機概念。

1. 在公司內部網路 ssh linux 主機後，再 ssh 另外一台主機，複製檔案也超麻煩的。
我之前有用過[SSH Tunnel](https://malagege.github.io/blog/2018/12/13/ssh-port-forward%E9%80%A3MySQL%E6%96%B9%E6%B3%95/)，仔細想想這個也可以用 ProxyJump 做連線操作。


2. VPN 登入進去後，遠端(mstsc) Window 跳板主機，該跳板主機有相關xshell 可進行連線操作

Bastion Host 我有爬到網路，可以安裝SELINUX環境下，因為這樣權限會比較嚴謹，可能會比較安全。