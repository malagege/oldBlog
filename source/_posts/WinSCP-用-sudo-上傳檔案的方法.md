---
title: WinSCP 用 sudo 上傳檔案的方法
date: 2021-01-10 21:08:46
tags: [winscp]
categories: Linux
---

用了這麼久都不知道
記錄一下

<!--more-->

請到裡面文章查看流程
[Harry 程式筆記: [WinSCP] sftp下使用sudo上傳檔案](https://harry-lin.blogspot.com/2019/05/winscp-sftpsudo.html) {% asset_link web1.png 備份圖 %}


想進去root 再切換 SVP ...
結果不能切換成功

或使用 [Linux創建用戶後，切換用戶報This account is currently not available_wangmm0218的博客-CSDN博客](https://blog.csdn.net/wangmuming/article/details/41575873)


## 切換 group 


[Set effective/primary group of login? :: Support Forum :: WinSCP](https://winscp.net/forum/viewtopic.php?t=11761)

使用 `newgrp <group>`(尚未測試)