---
title: API 防重放小記(replay attack)
date: 2021-06-02 20:32:43
tags: [api,replay,attack]
categories: 程式心法
---

最近思考怎麼預防尾造 API 回傳內容，剛好看到這幾篇文章
順便整理一下

<!--more-->

* [数据安全（反爬虫）之「防重放」策略 - SegmentFault 思否](https://segmentfault.com/a/1190000021922705)
* [网络安全问题-防篡改、防劫持、防重放_越学越菜的博客-CSDN博客](https://blog.csdn.net/qq_43323585/article/details/106088470)
* [开放平台API接口安全策略汇总 - 简书](https://www.jianshu.com/p/6105b06aa208)
* [Spring Boot接口如何设计防篡改、防重放攻击 – 前端开发，JQUERY特效，全栈开发，vue开发](https://www.jqhtml.com/49154.html)



簡單來說就是要做 sign 簽章，兩邊用一樣私鑰把內容加密(做簽章)，資料傳到Server 再把私私鑰做簽章，看sign傳過來的值有沒有一樣

這時候我有想到兩邊問題，私鑰被知道不就無解，任何人不就能使用
這邊防禦是以中間人為考量，並非討論App是否被破解取得私鑰
也有想過用公鑰、私鑰做一次加密解密，還時會被APP解析破解取得加密密碼

這時候加入 token 能有效杜絕這個問題(不是開放API的話)
api Token 是無狀態，自然可以解決上面問題
[session/token (http是无状态协议) - 簡書](https://www.jianshu.com/p/27317bd226fe)