---
title: SSRF 風險
date: 2022-07-30 23:45:37
tags: [ssrf]
categories: 源掃風險
---


小記

<!--more-->

## Server Side Request Forgery Prevention

[Server Side Request Forgery Prevention - OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html#case-1-application-can-send-request-only-to-identified-and-trusted-applications)

上面這篇推薦看，這邊小記
###  Case 1 - Application can send request only to identified and trusted applications

這邊我們看常常呼叫API，可能會在參數帶網址，程式去做呼叫API問題。但沒有限制API URL 檢核，可能會讓使用者作惡意事情。

解決方法有兩個層面

#### Application Layer:

1. 字串檢核，使用regex。
2. IP Address 驗證。可參考文章[Server Side Request Forgery Prevention - OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html#ip-address)
![](https://i.imgur.com/rM29CQt.png)


3. Domain 驗證。[Server Side Request Forgery Prevention - OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html#domain-name)

![](https://i.imgur.com/lKWyyl8.png)

4. 不要使用參數過來的完整URL。帶後面參數

#### Network layer: 

使用防火牆做防禦。


其他文章:

[4.4. SSRF — Web安全学习笔记 1.0 文档](https://websec.readthedocs.io/zh/latest/vuln/ssrf.html)