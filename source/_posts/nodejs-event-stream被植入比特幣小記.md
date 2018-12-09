---
title: nodejs event stream被植入比特幣小記
date: 2018-11-29 20:49:10
tags: 
categories: JavaScript
---

最近npm的`event stream`吵得沸沸揚揚的
竟然有人偷偷植入挖幣程式在裡面
只要有import都會中標
這篇指記錄有沒有中標

<!--more-->

```bash
npm ls event-stream flatmap-stream
```
```bash
npm ls -g event-stream flatmap-stream
```

{% asset_img 1.png 指令 %}

參考來源:[吃瓜群众带你了解 NMP 包 event-stream 被植入比特币后门的来龙去脉-前端开发博客](http://caibaojian.com/event-stream.html)

更多八卦....
[月下载量千万的 npm 包被黑客篡改，Vue 开发者可能正在遭受攻击 - 知乎](https://zhuanlan.zhihu.com/p/50938220?fbclid=IwAR01keb43yqDo_1AqH9WMGpGiDzmSx47TfDuoneElZmKDKsPmi-TrFuFqf0)
