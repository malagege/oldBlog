---
title: 正確控制 Window 麥克風 聲音裝置預設/預設通訊裝置
date: 2021-10-09 23:15:03
tags:
categories:
---

# 正確控制 Window 麥克風 聲音裝置預設/預設通訊裝置

最近電腦輸入輸出都很不穩定，上次的經驗[電腦使用藍芽耳機時候不能聽到電腦音樂(或其他聲音) | 程式狂想筆記](https://malagege.github.io/blog/2020/11/12/%E9%9B%BB%E8%85%A6%E4%BD%BF%E7%94%A8%E8%97%8D%E8%8A%BD%E8%80%B3%E6%A9%9F%E6%99%82%E5%80%99%E4%B8%8D%E8%83%BD%E8%81%BD%E5%88%B0%E9%9B%BB%E8%85%A6%E9%9F%B3%E6%A8%82-%E6%88%96%E5%85%B6%E4%BB%96%E8%81%B2%E9%9F%B3/)遇到藍芽有兩個裝置，導致輸出音樂可能會造成聽不到聲音現象。


<!--more-->

檢查

1. Windows 10 麥克風權限
2. 驅動檢查/麥克風檢查

大多網路上都是說檢查這幾項，詳細可以更多檢查可以看[Discord 視訊與語音故障排除指南 – Discord](https://support.discord.com/hc/zh-tw/articles/360045138471?utm_source=discord&utm_medium=blog&utm_campaign=2020-06_help-voice-video&utm_content=--t%3Apm)


## 聲音裝置預設/預設通訊裝置

正常程式會有預設裝置

![](https://i.imgur.com/ocVGSlr.png)

連接藍芽發現不是我正常要的預設裝置，雖然可以手動調整，但每次用也都很麻煩。

這邊分享我使用的方式，麥克風地方「預設裝置/預設通訊裝置」都設為同一個麥克風。但輸出預設不會用到通訊裝置，這一點也很奇怪...
輸出假如是用藍芽耳機的話，我目前是指定輸出到藍芽耳機。


我有看到[TIL: Discord doesn't use the default communications device : discordapp](https://www.reddit.com/r/discordapp/comments/4eq7au/til_discord_doesnt_use_the_default_communications/?sort=old)，這篇有提到Discord 預設裝置不是走通訊裝置，Line APP 也是這樣。目前應用程式大多數無法控制到預設通訊裝置，這邊可能需要注意。




## 麥克風聆聽輸出到裝置上

![](https://i.imgur.com/rT4Ccp0.png)

上面聆聽此裝置可以打勾，透過此裝置選擇要輸出的地方就可以聽到你麥克風講話的聲音。


## 線上測試麥克風

[線上網路攝影機＆麥克風測試 - ToolsKK](https://www.toolskk.com/webcam-mic-test)

後續有想到甚麼再補

## 其他

[Realtek 立體聲混音/麥克風 差別 - Mobile01](https://www.mobile01.com/topicdetail.php?f=500&t=5407415)


[Can’t Hear Anyone On Discord, Audio Not Working (FIXED)](https://www.addictivetips.com/web/cant-hear-discord/)