---
title: 登入開源系統(包含有2FA)整理
date: 2022-02-13 23:31:05
tags: [2fa,sso]
categories: 程式心法
---

最近找個 SSO 登入找有 2FA 登入驗證，要找資料不多，但查到相關軟體，就找出一大堆。所以先整理一下，以後要找直接回來看。

<!--more-->

## authentik

[Welcome | authentik](https://goauthentik.io/)

功能看起來很多，不過有爬文聽說Ram吃到700MB，因為要跑在樹梅派上面，所以就沒有使用這個，程式是用 Python。


## Keycloak

好像是老牌，是用Java開發的。

這邊下面有講到很多，下面內容就參照這篇文章[Day06 - 【入門篇】Keycloak的替代品 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10269542)。


## authelia

目前使用這個在樹梅派上面，聽說使用量也不高，目前跑得還穩穩地。


## 其他登入沒

### Casbin、Casdoor 

看到 Github Issue 有寫沒支援 2FA 功能。

[Casbin · An authorization library that supports access control models like ACL, RBAC, ABAC for Golang, Java, C/C++, Node.js, Javascript, PHP, Laravel, Python, .NET (C#), Delphi, Rust, Ruby, Swift (Objective-C), Lua (OpenResty), Dart (Flutter) and Elixir](https://casbin.org/)





那篇還滿多的，就簡單成列出來，有機會用到再回來補。

- okta
- CURITY
- IdentityServer
- CAS
- LemonLDAP
- OpenAM