---
title: JVM 查看記憶體使用情況方法
date: 2021-01-04 23:18:56
tags: [jvm]
categories: Java
---



<!--more-->

## 使用清單

```bash
#  檢視當前機器上所有執行的java程序名稱與pid(程序編號)
jps -l
#  顯示指定的jvm程序所有的屬性設定和配置引數
jinfo pid
```

TODO: 整理

[利用jmap和MAT等工具查看JVM运行时堆内存 - osc_3wmngxzc的个人空间 - OSCHINA - 中文开源技术交流社区](https://my.oschina.net/u/4416802/blog/3845052)
[JVM 記憶體溢出 - 夜升筆談](https://blog.yosheng.tw/jvm-out-of-memory)

[JVM故障分析及性能优化实战(VII)——使用MAT的Histogram和Dominator Tree定位溢出源 - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/article/1518274)

[JVM(完成度95%,不斷更新) - ⎝⎛CodingNote.cc ⎞⎠](https://codingnote.cc/zh-tw/p/152324/)

[JVM引數對J2EE效能優化的影響 - IT閱讀](https://www.itread01.com/p/580598.html)