---
title: 串接 LINE message API(WebHook)  和 LIFF 小記
date: 2020-04-16 21:02:29
tags: [LIFF]
categories: LINE
---

TODO: 尚未整理完成

<!--more-->

## 建議要使用同一組帳號開發

不同人的建立 Line project 的時候，**同一個人產生的 userid 會不一樣**。
但是同一個建的多個 project 會產生相同的 userid。
再串接上比較不會

## webhook 

push

reply

follow

unfollow

webhook


### 注意事項

驗證憑證

[Messaging API reference | LINE Developers](https://developers.line.biz/en/reference/messaging-api/#response)


## LIFF 

### 注意事項

token 關掉註銷

email 權限
[Integrating LINE Login with your web app | LINE Developers # Applying for email permission](https://developers.line.biz/en/docs/line-login/integrate-line-login/#configuring-your-channel)


[開發LINE聊天機器人不可不知的十件事 - LINE ENGINEERING](https://engineering.linecorp.com/zh-hant/blog/line-device-10/)
[LINE Bot 系列文 — 什麼是 Webhook? - Justin Lee - Medium](https://medium.com/@justinlee_78563/line-bot-%E7%B3%BB%E5%88%97%E6%96%87-%E4%BB%80%E9%BA%BC%E6%98%AF-webhook-d0ab0bb192be)
[21CS學習網](http://www.21cs.tw/Nurse/showLiangArticle.xhtml?liangArticleId=503)


## 測試內網對外 API 工具 

ngrok

localtunnel 

[用 localtunnel 幫 chatbot 上 https | 筆記國度](https://taichunmin.idv.tw/blog/2020-06-13-linebot-localtunnel-https.html?fbclid=IwAR3eNChmEIJnp7FwVp-sJ4yUifeVRyRmFDa_GNWojjO1vhPFZ3PQIB_dyVY)


## 表情符號

舊版使用特殊文字產生表情符號
[LINE BOT の emoticon の説明がよく分からなかったのでリストにしました。 - Qiita](https://qiita.com/Repomn/items/b991f20e4fecd0577d4b)


新版
[[Golang][LINE][教學] 如何傳送 LINE 專屬的表情符號(emoji)](http://www.evanlin.com/go-emoji/)
[關於 LINE Emoji 的一些細節（以 Golang 為例) - LINE ENGINEERING](https://engineering.linecorp.com/zh-hant/blog/line-emoji-with-golang/)

老實說，我覺得舊版還是比較方便....