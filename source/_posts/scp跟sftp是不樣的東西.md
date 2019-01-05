---
title: scp跟sftp是不一樣的東西
date: 2018-04-16 20:35:20
tags: [scp,sftp]
categories: 程式心法
---

最近在公司做使用PHP做sftp傳輸上去
結果一直出問題
使用`ssh2_scp_send`傳輸檔案
公司內部測試可以傳檔案
但別的主機卻不能

<!--more-->

我在我的主機安裝ssh2環境（改天來補筆記）
發現還是不能連到那台

後來我決定，試著打 sftp command line連過去
看到顯示`freeFTPd`，我就找相關資料
freeFTPd是window平台，可以免費架設sftp/ftp東西
不過我爬到...

>SFTP 雖然提供了一個安全可靠的傳送渠道，但是SFTP不能直接用瀏覽器開啟。需要使用支援SFTP的軟件才能進入SFTP Server，例如CuteFTP、WinSCP、或者Client Side的FileZilla。還有，freeFTPd的SFTP是不能用SSH登入及使用SCP傳送檔案的。
[在Windows下使用freeFTP來架設SFTP Server & 我是我，葉子 - yzl1218 # 天空部落 TIAN #](https://yzl1218.tian.yam.com/posts/62969137)


有找到使用`copy("ssh2.sftp://{$ftp_IP}".$remote, $local);`就可以用了
[PHP-sftp文件上传-11750810-51CTO博客](http://blog.51cto.com/11760810/2044534)

我一直以為SCP跟SFTP是差不多東西
但結果證明是不一樣的
SCP沒有續傳功能，有點類似做`cp`指令動作透過ssh

>sftp是基於ftp使用ssh
SCP和CP命令相似，SFTP和FTP的使用方法也類似。SCP和SFTP的共同之處在於「使用SSH將文件加密才傳輸的」
使用「WinSCP」或者「FileZilla」之類的客戶端，還可以和Windows之間進行文件傳輸。 

>SCP和SFTP的不同之處，首先就是之前提到的，SCP使用「SCP命令」，SFTP則類似「FTP處理文件」的使用方式。
它們的不同之處還不止如此，還有「SCP比較簡單，是輕量級的，SFTP的功能則比較多」。
詳細說的話，雖然還有很多不同之處，但2者的最大不同之處在於「SFTP在文件傳輸過程中中斷的話，連接後還可以繼續傳輸，但SCP不行」。
[SCP和SFTP區別 - - ITeye博客](http://wb8206656.iteye.com/blog/2231060)

經過這次....
下次應該不會再踩這個雷了吧
