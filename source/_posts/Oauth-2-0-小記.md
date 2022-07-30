---
title: Oauth 2.0 小記
date: 2022-07-31 00:55:20
tags: [oauth]
categories: 程式心法
---

Oauth 2.0 小記，有些沒紀錄完整，有空回來補。

<!--more-->

## 上課小記

怕課堂上的課忘記或先前錯誤觀念，簡單小記。

OAuth 2.0 常見有四種流程

1. Authorization Code Flow

簡單敘述，最安全，使用者  Resouce Owner 是人，前面主要先取得授權碼(Authorization Code)再跟 Client(AP Server)去跟 Oauth Provider 去要 token 動作，使用者不會知道 token 資料。state 是 client 要去做判斷，防止 DDOS 被破解攻擊。

2. Implicit Flow

通常純前端使用，但沒有用到 Secret ID ，因為用了就不安全。相對密碼就給別人看，純前端使用 Authorization Code Flow 非常危險。
這個相對第一個流程沒有取得 Authorization Code 去換，當然安全性會比第一種方法危險一點。(如意被XSS 攻擊，讓使用者 token 惡意 JS 被偷走)

沒有 refresh token 。

3. Resource Owner Password Flow

不需要 refresh token。Client 讓使用者輸入帳號、密碼，最後程式做完要清除掉，此方法已經在 Oauth 2.1 被廢除。

4. Client Credentials Flow

當 Resource Owner 不是人，程式進行溝通不需要做授權確認，可採取方式。有點像帳號密碼登入。

## 相關名詞筆記

OIDC(OpenID Connect)

接近使用 OAuth 2.0 模式實作，OAuth 2.0 取得 Token(Access Token)，可一併取得 ID TOKEN。

ID TOKEN
是一種 JWT 格式，裡面存放使用者資料。

JWT
一種簽章過格式，可以驗證 payload 是否被串改，達到資料可驗證(使用私鑰)。但這不是對資料加密。

可以把 JWT 貼到[JSON Web Tokens - jwt.io](https://jwt.io/)上面資訊看內容。

URL Fragment
`http://xxxx/#abc=123`，透過 hash 使用帶一些內容，**但是這些內容不會傳到後端**。

Implicit Flow 最後回傳接收參數是使用`URL Fragment`，這種方式資料不會傳送到 server 上面。


Device 
Client(指的電視、硬體裝置)會先去 Authorization Endpoint 去要依些資料，如Device Code、驗證碼等等資訊。

可參考:[Authorizing OAuth Apps - GitHub Docs](https://docs.github.com/en/enterprise-server@3.2/developers/apps/building-oauth-apps/authorizing-oauth-apps#device-flow)


IdentityServer4 ，正常 Auth Provider 不會有註冊會員功能，他只有時實作使用者登入、權杖(OAuth、OIDC)。剛開始我以為可以不用做註冊密碼，因該這些功能都會包進去，但老師說正常不會有這些實作，有點像 Middleware 概念，我後來有仔細思考，確實不該有這個功能。

可以看這張圖[The Big Picture — IdentityServer4 1.0.0 documentation](https://docs.identityserver.io/en/latest/intro/big_picture.html#how-identityserver4-can-help)，結論還是乖乖做註冊會員XD。


網路找到現有範例[benscabbia/identityserver4-dockersample-dotnetcore-nginx: IdentityServer4, ASP.NET Core, NGINX, HTTPS, Docker Compose](https://github.com/benscabbia/identityserver4-dockersample-dotnetcore-nginx)


## 細說流程圖

### Authorization Code Flow

Resource owner => 人
User-agent => 瀏覽器(Chrome)
Client => WEB 後端程式

![](https://i.imgur.com/RxIbXXv.png)
圖片參照:[OAuth 2.0 and OIDC workflows - IBM Documentation](https://www.ibm.com/docs/sk/sva/9.0.4?topic=SSPREK_9.0.4/com.ibm.isam.doc/config/concept/con_oauth20_workflow.html)

1. user-agent 瀏覽器會發送 Request 到 Client 後端程式。Client 會導向 (Redirect) Authorization Server。

![](https://i.imgur.com/3L1LFAU.png)


2. 如果使用者未登入到 Authorization Server，會讓使用者登入資訊，在做授權動作。

![](https://i.imgur.com/MqEI5P5.png)


3. 授權完動作，使用者拿到 Authorization code ，傳送到Client程式。

![](https://i.imgur.com/bUANaxm.png)


4. Client 程式會發送 authorization code 和 redirect URI。

5. 取得 Access Token ，這邊看文件有沒有 refresh token。

4和5流程就不詳細化畫流程。

### Implicit 隱碼

![](https://i.imgur.com/LZdP0bt.png)

詳細流程可看:[OAuth 2.0 筆記 (4.2) Implicit Grant Flow 細節 – Yu-Cheng Chuang’s Blog](https://blog.yorkxin.org/posts/oauth2-4-2-implicit-grant-flow.html)

簡單記錄我的小記

(A). 最下面紅色箭頭(User-Agent訪問 Client)，回傳到User-Agent，導向 Authorization Server 。藍線 Redirect 到 User-Agent 讓使用者(Resource Owner) 做登入動作。

(B). 使用者(Resource Owner)輸入帳號密碼，導向Authorization Server 做授權動作。

(C). 授權後會 Redirect URI with Fragment 到 User-Agent。

(D). 導向 Web 前端頁面，注意 URL (#)後面資訊不會傳到 Server上面。

(E)(F). Script 去抓 Access Token。

(G).  Access Token 訪問 Resource Server。

#### parse location (fragment 資料)
https://stackoverflow.com/questions/12271736/javascript-access-to-location-headers-url-fragment-for-oauth-2


### Client Credentials Grant

![](https://i.imgur.com/bFukCEq.png)


可參考:[OAuth 2.0 筆記 (4.4) Client Credentials Grant Flow 細節 – Yu-Cheng Chuang’s Blog](https://blog.yorkxin.org/posts/oauth2-4-4-client-credentials-grant-flow.html)

簡單說明就是，Resource Owner 不是人，和當下使用者無法做 Authorization 時候，可以採用這個模式。


## Device Code

User Code 指的是驗證碼。

1. Client 使用 Secret Id 去訪問 Authorization Server 取得 Device_id & User Code。

2. 導頁 user 讓使用者輸入驗證碼頁面

3. 持續 pull client_id,device_code,grant_type=device_code 訪問 Authorization Server 取得 Access Token

[Step 1: App requests the device and user verification codes from GitHub](https://docs.github.com/en/developers/apps/building-oauth-apps/authorizing-oauth-apps#step-1-app-requests-the-device-and-user-verification-codes-from-github)

## 查看特別案例


### Line Notify 手機頁面自動登入


Line Notify 手機點選登入，可以自動登入，我發現他是使用 Line APP 做登入的。注意這邊我發現要用預設瀏覽器才不會有問題，導向 LINE APP時候再跳回瀏覽器是開「預設瀏覽器」，才能做到自動登入。目前猜測是state寫在sessions那邊。

### state 可使用 session_id

可看上參考，沒有expire，可以多存變數使用。

[State parameter](https://pipedrive.readme.io/docs/marketplace-oauth-authorization-state-parameter)





## 彩蛋

[Electron系列文章 - deeplink打开应用 - FlyKnows](http://www.flyknows.com/2020/08/13/deeplink%E6%89%93%E5%BC%80%E5%BA%94%E7%94%A8/)


[【雜談】 今天就來點閒聊吧② - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10281440)
[OAuth.com - OAuth 2.0 Simplified](https://www.oauth.com/)