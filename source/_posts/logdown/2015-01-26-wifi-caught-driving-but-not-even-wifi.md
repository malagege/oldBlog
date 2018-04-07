---
layout: post
title: 'wifi抓到驅動但不能連wifi'
date: 2015-01-26 03:06
comments: true
categories: Windows
tags: [wifi,驅動,不能連wifi]
---
今天幫主任重灌筆電，由於是用我同學整合的Win7安裝
發現驅動抓到無線網卡，但無線網卡沒法找無線Wifi


```
1.點擊開始菜單，在搜索欄中輸入「services.msc」（輸入時不要打引號），並按下回車。如果此時彈出用戶賬戶控制窗口，請您點擊「繼續」。
2.分別按順序雙擊打開"CNG Key Isolation" "Extensible Authentication Protocol" "WLAN AutoConfig"服務。點擊「常規」選項卡，確保「啟動類型」是「自動」或者「手動」。然後點擊「服務狀態」下面的「啟用」按鈕。

或者手動啟動三個服務
1.
"CNG Key Isolation"
2.
"Extensible Authentication Protocol"
3.
"WLAN AutoConfig"

```
http://rossic0989.pixnet.net/blog/post/29726514-%E7%84%A1%E6%B3%95%E5%95%9F%E5%8B%95wlan-autoconfig%E9%8C%AF%E8%AA%A4%E4%BB%A3%E7%A2%BC1068%28win7%29

結果是我同學Win7整合服務關掉orz
讓我搞這麼久