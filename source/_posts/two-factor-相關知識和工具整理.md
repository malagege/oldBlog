---
title: two factor 相關知識和工具整理
date: 2022-02-13 23:40:08
tags: [2fa]
categories: 程式心法
---

最近公司VPN用 2FA 驗證，用 Google Authenticator，原本想說這個是透過網路，連 Google 伺服器去取密碼，結果手機開飛航模式發現密碼還是可以產生出來，就激發我的興趣，簡單整理資料。

<!--more-->

## 原理

[[OneTimePassword] 一次性密碼演算法：簡介HOTP、TOTP和Google Authenticator | 全端開發人員天梯](https://fullstackladder.dev/blog/2017/09/07/one-time-pass-introduce-with-hotp-totp-google-authenticator/)

演算法:
1. HOTP(計數器)
2. TOTP(時間)

[配置 HOTP 一次性密码机制 - IBM 文档](https://www.ibm.com/docs/zh/sva/9.0.4?topic=authentication-configuring-hotp-one-time-password-mechanism)

QrCode: 
```
otpauth://totp/example.com:john?algorithm=SHA1&digits=6&issuer=example.com&period=30&secret=EI6UXYUKSLSMKVQTTRS474MHUTFV4UPTANXYOZV3IME7K7GQ3UDA
```

## 相關工具

後來發現除了用手機APP，其實拿到金鑰也能產生，這邊就不研究了。有回來操作再補。

[ssh - Is there a command line two-factor authentication verification code generator? - Server Fault](https://serverfault.com/questions/519956/is-there-a-command-line-two-factor-authentication-verification-code-generator)

[2016台網中心電子報](http://www.myhome.net.tw/2016_01/p24.htm)

[Your Time-based One time passwords from your terminal | by Jean-Thierry Bonhomme | Medium](https://medium.com/@jtbonhomme/your-time-based-one-time-passwords-from-your-terminal-e9955fbc9b65)

[totp-cli | TOTP CLI tool written in Go.](https://yitsushi.github.io/totp-cli/)