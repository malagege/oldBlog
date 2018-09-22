---
title: vscode嘗試使用ftp套件心得
date: 2018-08-20 20:51:28
tags: [vscode,sftp,ftp]
categories: 實用工具
---

Sublime Text 有一個很好用sftp套件
但最近換到vscode
很缺一個方便看ftp
嘗試了3個ftp套件
[ftp-kr - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ruakr.ftp-kr#review-details)
[ftp-simple - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=humy2833.ftp-simple#review-details)
[sftp - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=liximomo.sftp#review-details)

<!--more-->

有稍微網路爬一下[Visual Studio Code几款FTP插件使用总结 - 我的笔记 - SegmentFault 思否](https://segmentfault.com/a/1190000012875114)
好像沒有好用套件
我也沒有需要本地端通部上傳到ftp
希望手動上傳

## [ftp-kr - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ruakr.ftp-kr#review-details)
網路上看到人家推的
基本ftp好像不錯用
不過上傳會一直跳出提示視窗(不會自動關掉...)
這點我還滿不喜歡
不過可以設定多個帳號也不錯
{% asset_img vscode_ftp_kr.gif "Remote Explorer" %}

## [ftp-simple - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=humy2833.ftp-simple#review-details)
也上網路上看到的
好像有看到有一篇說很像Sublime Text 的sftp套件，但是我覺得不像....
不過他有一個特點，上傳模式都是手動上傳
額且上傳本地端檔案名稱可以上傳ftp上面不一樣的檔名
這還有點神奇
假如喜歡手動設定多樣需求
這個一定很好用
{% asset_img vscode_ftp_simple.gif "ftp-simple" %}


## [sftp - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=liximomo.sftp#review-details)
接下來這一套是往自己搜尋到的
vscode預設搜尋好像照星星數排的
但是`ftp @sort:installs`搜尋下載數最高是這個
嘗試結果，看到有**ftp diff功能**，這個功能我覺得還滿好用的
基本上傳也不會跟[ftp-kr - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ruakr.ftp-kr#review-details)彈出東西
而且有一個**Remote Explorer**
可以不用再開filezilla看了XD
{% asset_img remote-explorer.png "Remote Explorer" %}
{% asset_img vscode_sftp.gif "sftp" %}


## 心得感想

其實這三套嚴格說都不差
目前個人排名
`sftp > ftp-simple > ftp-kr`

