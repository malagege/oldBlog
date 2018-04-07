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


參考來源:
[鳥哥的 Linux 私房菜 -- 第十三章、Linux 帳號管理與 ACL 權限設定](http://linux.vbird.org/linux_basic/0410accountmanager.php#userswitch)

