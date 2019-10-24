---
layout: post
title: '[Linux]切換使用者筆記'
date: 2017-12-13 14:20
comments: true
categories: Linux
tags: [Linux,su,sudo]
---
最近忘記這篇[Controb排程設定 « 進擊的程式新手](http://malagege.logdown.com/posts/2131881)之前下的指令要怎麼用
所以記錄一下
以免在次花一堆時間查詢
```bash
 sudo -u debian-transmission crontab -e
```

<!--more-->

```
[root@study ~]# su [-lm] [-c 指令] [username]
選項與參數：
-   ：單純使用 - 如『 su - 』代表使用 login-shell 的變數檔案讀取方式來登入系統；
      若使用者名稱沒有加上去，則代表切換為 root 的身份。
-l  ：與 - 類似，但後面需要加欲切換的使用者帳號！也是 login-shell 的方式。
-m  ：-m 與 -p 是一樣的，表示『使用目前的環境設定，而不讀取新使用者的設定檔』
-c  ：僅進行一次指令，所以 -c 後面可以加上指令喔！
```

先`sudo -i`進入root權限，再下`su -m debian-transmission`指令就可以進去shell介面

```
[root@study ~]# sudo [-b] [-u 新使用者帳號]
選項與參數：
-b  ：將後續的指令放到背景中讓系統自行執行，而不與目前的 shell 產生影響
-u  ：後面可以接欲切換的使用者，若無此項則代表切換身份為 root 。
```

**2019-10-24**

## 切換使用者(帳號)方法

> 先`sudo -i`進入root權限，再下`su -m debian-transmission`指令就可以進去shell介面

除了這招，最近 docker 執行 cmd 的方法讓我想到一個方法`sudo -u debian-transmission bash`

## newgrp, groups

之前有紀錄 [Linux 利用 usermod 修改使用者的參數和資料 | 程式狂想筆記](https://malagege.github.io/blog/2014/09/15/logdown/2014-09-15-linux-use-usermod-to-modify-the-users-parameters-and-data/)
但當時還菜~~現在也很菜~~
沒有紀錄很完整

例如剛剛安裝完 docker
只能用 root 方法去執行
如果想把一般 user 使用 docker 方法，就把 user 加到 docker 使用者

```
sudo usermod -G docker -a testuser
```
[[Docker] 使用非 root 權限來執行 docker client | EPH 的程式日記](https://ephrain.net/docker-%E4%BD%BF%E7%94%A8%E9%9D%9E-root-%E6%AC%8A%E9%99%90%E4%BE%86%E5%9F%B7%E8%A1%8C-docker-client/)
例如這個

要重開 bash 或 `newgrp docker`可以使用
~~當年就是看太快，想說奇怪怎麼沒用~~

詳細講一下 newgrp docker，其實我覺得他有點向開一個新 bash
如果很難懂看下面應該很好懂
groups 第一個就是目前群組

> newgrp 切換群組
> 此為帳號和群組管理相關的指令, newgrp 指令類似 login 以相同的帳號登入另一個群組,以指令 exit 又回到原群組。
> $ groups ←列出有那些有群組成員
> frank marketing ←有效群組(effective group)目前為〝frank〞(列在前的為有效群組)
> $ newgrp marketing ←登入〝marketing〞群組
> $ groups ←驗證有無登入另一個群組
> marketing frank ←有效群組變〝marketing〞了
> $ exit ←回原群組
> $ groups ←驗證看看
> frank marketing 
> 參考 [阿旺的 Linux 開竅手冊 newgrp 指令](http://www.polish.url.tw/inst/newgrp.html)

再看[Linux newgrp命令用法详解：切换用户的有效组](http://c.biancheng.net/view/860.html)圖 1 newgrp 切換初始組程序運行過程圖
應該可以更清楚了解

## 我想的管理帳號方法

Transmission 會建立 debian-transmission 帳號。
Docker 會建立 docker 帳號。

所以一個應用程式獨立一個帳號
然後利用加群組方式授權帳號
可以算是一個管理模式
這樣不用再注意777的問題

參考來源:
[鳥哥的 Linux 私房菜 -- 第十三章、Linux 帳號管理與 ACL 權限設定](http://linux.vbird.org/linux_basic/0410accountmanager.php#userswitch)
[技术|chgrp 和 newgrp 命令简介](https://linux.cn/article-11399-1.html)
