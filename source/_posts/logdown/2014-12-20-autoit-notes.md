---
layout: post
title: 'Autoit筆記'
date: 2014-12-20 06:58
comments: true
categories: AutoIt
tags: [AutoIt,自動化]
---
由於最近多了一項收發文工作，每天都要開起電腦收發文程式。
我就想想有沒有讓程式自動化，這樣我以後就開機就好了XD
於是我就開始做了
<!--more-->

目前我只知道有這些
1. Sikuli Script
2. 按鍵精靈
3. Autoit

這三個我只用過Sikuli，當出使用Sikuli的原因是因為對Python有好感
而且用圖來判斷有很炫的感覺XD，但後來他慢慢升級在Windows上會有怪問題，所以這次也不考慮用它。
Autoit在以前SuperXP、AutoXP，那個時代還滿有名的自動安裝軟體。
而且那一台電腦是Windows 8，查了一下Autoit在Win 8(desktop mode)可以正常運行，又能轉exe，所以就決定用它
以前不太會寫程式，教學文件幾乎都是英文的(英文苦手:X)
現在有基本程式基礎，所以比較看的懂教學文件，不過我依然還是英文苦手orz

SciTE Script Editor，還滿好用了
隨便輸入幾個關鍵字就會跑出選單
目前有用幾個相關指令

1. Run
2. WinWaitAct
3. ControlClick
2. ControlSend

這4個函式就能滿足我的需求

編寫時用AutoIt Window Info拉Finder Tool出來到按鈕
在Control下方的Advanced Mode可抓出CLASS名稱和INSTANCE
`ControlClick("程式標題名稱","程式按鈕文字",[CLASS:Button; INSTANCE:2])`

大部份函式都機乎是這樣

最後發現執行有動到滑鼠鍵盤可能會失效
爬文找到一個方法可以鎖住鍵盤滑鼠
`BlockInput(1);鎖定鍵盤滑鼠
BlockInput(0);解除鎖定鍵盤滑鼠`

程式寫錯假如你有BlockInput(1)不能做動作的話
別急著按電腦重開機，只要`Crontol+Alt+Del`兩次
就可以解除鎖定了

-------
參考：

自動安裝程式 - 試試AutoIt
http://blog.roodo.com/sherwin/archives/71806.html

AutoIt 【參考資料2009/09/28】
http://id8051.pixnet.net/blog/post/29407939-autoit-%E3%80%90%E5%8F%83%E8%80%83%E8%B3%87%E6%96%992009-09-28%E3%80%91

請問如何禁用滑鼠?[已解決]
http://www.autoitx.com/viewthread.php?tid=13953

http://www.autoitx.com/index.php

發現好東西 autoit
http://crazy.molerat.net/learner/cpuroom/net/reading.php?filename=100100995837.dov

桌面離線也可動作的AutoIt指令：ControlSend跟ControlClick
http://pulipuli.blogspot.tw/2011/05/autoitcontrolsendcontrolclick.html

[A] AutoIt!! 註解與其它注意事項
http://edisonx.pixnet.net/blog/post/38335545-%5Ba%5D-autoit!!-%E8%A8%BB%E8%A7%A3%E8%88%87%E5%85%B6%E5%AE%83%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A0%85

**2020-09-11**

Window info

- [AutoIT 自動化完成程式安裝 @ JosephPhoto 的 網路日誌我的網誌 :: 隨意窩 Xuite日誌](https://blog.xuite.net/josephtsai1126/wretch/193271565-AutoIT+%E8%87%AA%E5%8B%95%E5%8C%96%E5%AE%8C%E6%88%90%E7%A8%8B%E5%BC%8F%E5%AE%89%E8%A3%9D)

管理者模式

- [Keyword #RequireAdmin](https://www.autoitscript.com/autoit3/docs/keywords/RequireAdmin.htm)
- [Run as administrator ? - AutoIt General Help and Support - AutoIt Forums](https://www.autoitscript.com/forum/topic/134675-run-as-administrator/)
- [Autoit 使用 runas 執行沒有反應 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/questions/10185977)

放大縮小

- [AutoIT: WinSetState可以定义窗口大小 - chenpassion - 博客园](https://www.cnblogs.com/autotest/p/3262234.html)



其他

- [J2TEAM/awesome-AutoIt: A curated list of awesome UDFs, example scripts, tools and useful resources for AutoIt.](https://github.com/J2TEAM/awesome-AutoIt)


FileInstall 

編譯 exe 
- [奔騰兔分享誌: AutoIt 執行外部程式進階應用](http://pentiumto.blogspot.com/2017/07/autoit_5.html)

通知

[Function TrayTip](https://www.autoitscript.com/autoit3/docs/functions/TrayTip.htm)

filedelete 跟暫存路徑

[Function FileDelete](https://www.autoitscript.com/autoit3/docs/functions/FileDelete.htm)
[Function _WinAPI_GetTempFileName](https://www.autoitscript.com/autoit3/docs/libfunctions/_WinAPI_GetTempFileName.htm)


程式是否開啟
[Function ProcessExists](https://www.autoitscript.com/autoit3/docs/functions/ProcessExists.htm)
[terminate process? - AutoIt General Help and Support - AutoIt Forums](https://www.autoitscript.com/forum/topic/71544-terminate-process/)