---
title: Shell Script 錯誤中斷方法
date: 2021-11-07 22:03:29
tags: [shell]
categories: Linux
---

錯誤不會中斷。

<!--more-->

![](https://i.imgur.com/5X5nTtm.png)


```
#!/bin/sh
set -e
```

> 解決方法十分簡單, 只要加上 “set -e” 一行, 那麼 Shell Script 執行指令遇到錯誤時, 便會立即終止執行, 並自動退出離開, 
參考:[Shell Script 遇到錯誤時自動退出離開](https://www.opencli.com/linux/shell-script-exit-on-error)


![](https://i.imgur.com/rLDmgdB.png)
