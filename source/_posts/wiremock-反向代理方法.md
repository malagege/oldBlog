---
title: wiremock 反向代理方法
date: 2021-03-31 21:06:59
tags: [mockapi, wiremock, java]
categories: 實用工具
---

最近寫好 mock api 完後，剛好開發環境也完成了
想直接在本機測試
想說 wiremock 有沒有反向代理方法
結果還真的有耶!!

<!--more-->

先前文章有用過

[Proxying - WireMock](http://wiremock.org/docs/proxying/)

```json
{
    "request": {
        "method": "ANY",
        "urlPattern": "/project/.*"
    },
    "response": {
        "proxyBaseUrl" : "http://localhost:8080/"
    }
}
```


[Stubbing - WireMock](http://wiremock.org/docs/stubbing/)

> HTTP methods currently supported are: GET, POST, PUT, DELETE, HEAD, TRACE, OPTIONS. You can specify ANY if you want the stub mapping to match on any request method.



未來有空來寫寫看 Rand 非特定字串(如:UUID,TOKEN之類的)

## Rand 

http://wiremock.org/docs/response-templating/