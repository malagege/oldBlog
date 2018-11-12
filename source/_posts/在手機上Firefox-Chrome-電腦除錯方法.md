---
title: 在手機上Firefox &Chrome 電腦除錯方法
date: 2018-10-08 21:15:36
tags: [Firefox,Chrome]
categories: 實用工具
---

最近公司遇到在Line上面開網頁
會空白頁面...(或正常頁面倒到那一台主機不會有跳頁)
被收到需要做手機上的Debug
當然之前有做過
不過來記錄一次記錄
因為不常做XD

<!--more-->


### 安裝 google adb driver
原本想安裝自家的Driver
不知道win7 x64好像不能用
後來選擇google adb driver
[小米5 ADB Interface 驅動程式安裝方式 - 小米手機5 - MIUI官方論壇](http://tw.miui.com/thread-24838-1-1.html)
[Get the Google USB Driver  |  Android Developers](https://developer.android.com/studio/run/win-usb)

由於有找到[[Android] Windows 安裝 Google 萬用 USB 驅動程式 | 熱血剪貼部](http://blog.ctrlxctrlv.net/adroid-google-usb-driver/)詳細文章({% asset_link "1.jpg" 備份圖 %})
所以就沒花費這一塊做筆記了

### Firefox安裝 Adb helper

[WebIDE Add-ons](https://ftp.mozilla.org/pub/labs/fxos-simulator/)


#### 開啟 WebIde
由上角正常可以看到手機名稱

(圖片待補)

手機點選`允許`
{% asset_img 2.jpg "Android上的Firefox" %}

就可以看console

### Chrome 除錯模式

[遠程調試 Android 設備使用入門  |  Tools for Web Developers  |  Google Developers](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/?utm_source=dcc&utm_medium=redirect&utm_campaign=2016q3)

{% asset_img 3.png "選單功能位置" %}

設定跟功能上比Firefox多
設定步驟更簡單，可以直接看到網頁圖示



最後憑證的問題還是不清楚為什麼orz