---
layout: post
title: 'Win7 的視窗閃動、間歇地出現"沒有回應" '
date: 2015-03-07 10:44
comments: true
categories: Windows
tags: [視窗閃動]
---
最近同學幫別人灌電腦遇到Win7裡的Office會有閃爍的問題
結果Google一下，還真的可以解決!!!
記錄一下，說不定哪天就用到了

```
一些 電腦優化程序 可能會把 電腦的關閉( Shutdown?? )速度 增加 ，
Win7 的視窗閃動、間歇地出現"沒有回應" ( 並非螢幕黑屏 ) 問題多數是由這所引起的 。

解 :
1 按左下角的"開始"，輸入 "regedit" ，開 "regedit.exe"，
   程式的左方應包含一 "資料夾選巴"。

2 在選巴中選 " HKEY_USERS \ .DEFAULT \ Control Panel \ Desktop "，
   程式的右方應出現一些 "登錄值" ，包含 名稱、類型、資料 等。

3 在 "登錄值" 中選取 " HungAppTimeOut " 和 " WaitToKillAppTimeOut "，點右鍵刪除。

4 在選巴中選 " HKEY_CURRENT_USER \ Control Panel \ Desktop "。

5 在 "登錄值" 中選取 " HungAppTimeOut "、" WaitToKillAppTimeOut "、" WaitToKillServiceTimeOut "，點右鍵刪除。

略解 :
1 開 登錄編輯程式( Registry Editor ) 。

2 刪除 " HKEY_USERS \ .DEFAULT \ Control Panel \ Desktop " 中的 (" HungAppTimeOut "、" WaitToKillAppTimeOut ")。

3 刪除 " HKEY_CURRENT_USER \ Control Panel \ Desktop " 中的 (" HungAppTimeOut "、" WaitToKillAppTimeOut "、" WaitToKillServiceTimeOut ")。

若發現此解適用於其它作業系統 ，請下報。

參自 : www.mydigitallife.info
```

參考來源：
Windows7 視窗、畫面、遊戲程式 閃動問題 解決辦法
https://groups.google.com/forum/#!topic/computer_resuce/rDn_21bC2Lk