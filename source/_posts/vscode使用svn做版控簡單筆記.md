---
title: vscode使用svn做版控簡單筆記
date: 2018-08-20 20:09:25
tags: [svn,vscode]
categories: 實用工具
---

由於公司舊專案都是用SVN去開發的
但最近...接觸的案子都是舊專案
平常看這次修改差異都要切到TortoiseSVN
但今天鼓起勇氣嘗試[SVN - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=johnstoncode.svn-scm)
在此做個筆記

<!--more-->

>If you use TortoiseSVN, make sure the option Command Line Tools is checked during installation and C:\Program Files\TortoiseSVN\bin is available in PATH.

想說我安裝目錄也在相同路徑，環境變數也有設定PATH
就直接安裝，裝完重啟竟然跑出沒有SVN訊息....

覺得怪怪的，有趣命令提示字元打`svn`，沒有反應
在想是不是沒安裝`command line`東西，試著重新安裝
果然有command line選項

{% asset_img vscode_svn.jpg "TortoiseSVN安裝選單" %}


然後vscode操作svn版本操作介面跟git一樣
只能說vscode太神了


我所有編輯器都混著用
從Sublime Text70% 到vscode 30%
現在到vscode 80%到Sublime Text 20%

不過大量文字編輯我還是習慣用Sublime Text


[SVN Gutter - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=beaugust.blamer-vs)
好像類似gitlens東西
不過有空再試驗看看