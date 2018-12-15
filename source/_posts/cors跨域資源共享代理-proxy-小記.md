---
title: cors跨域資源共享代理(proxy)小記
date: 2018-12-13 20:31:01
tags: [cors]
categories: 實用工具
---

最近使用cors anywhere
簡單使用
有遇到不該遇到的雷
這邊小記

<!--more-->

# CORS-ANYWHERE

官網：https://cors-anywhere.herokuapp.com/
Github：https://github.com/Rob--W/cors-anywhere/

可以到官網clone下來，執行node js


## 踩雷小記

原本我就簡單使用`fetch`呼叫cors-anywhere，但是一直失敗
其實我也是英文苦守
當下沒注意到有明顯提示

```
Missing required request header. Must specify one of: origin,x-requested-with
```

fetch並不會塞`x-requested-with`
當然在postman塞`x-requested-with`
就可以work了


## Corsproxy

可以全局安裝
```
npm install -g corsproxy
corsproxy
```

但一樣有上面的雷
說不定只有我會踩到orz

也可以去docker hub找

