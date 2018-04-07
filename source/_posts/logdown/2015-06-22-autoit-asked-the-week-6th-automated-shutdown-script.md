---
layout: post
title: '[Autoit]詢問禮拜六日自動關機腳本'
date: 2015-06-22 05:24
comments: true
categories: AutoIt
tags: [AutoIt,自動化]
---
就公文開啟腳本加上禮拜六日自動關機程式，不要浪費學校的電
然後怕卡在國定假日要來上課，所以寫出這個角本
自動開機就在BIOS設定，就搞定了XD

```vb xxx.au3
#include <Date.au3>

Func OpenProc()
   #Working code

EndFunc

If @WDAY==1 or @WDAY == 7 Then
   run("shutdown -s -f -t 600")
   sleep(5)
   $goSutdown = MsgBox($MB_OKCANCEL,@WDAY,"今天是周休假日，程式自動執行關機，請問你是否繼續執行關機？")
   If $goSutdown = $IDCANCEL Then
	  run("shutdown -a")
	  OpenProc()
   EndIf
   exit(1)
EndIf

OpenProc()

```