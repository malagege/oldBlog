---
layout: post
title: 'Exit Codes 程式回傳錯誤碼'
date: 2017-12-12 15:29
comments: true
categories: 程式心法
tags: [exit code,system.exit]
---
看到上一篇[Crontab 執行特定目錄 « 進擊的程式新手](http://malagege.logdown.com/posts/4442412-crontab-execution-specific-directory)
我就想想我寫的程式如何寫出err訊息
然後在Bitbucket測試程式可以跑

後來Google到[Exit Codes - Shell Scripting Tutorial](https://www.shellscript.sh/exitcodes.html)
原來每一個程式語言有類似`System.exit(int i)`這個東西
0表示程式正常，其他值表式程式有錯誤

這時候zsh前面會有x表示

[理解 Exit Code 並學會如何在 Bash 腳本中使用 - 壹讀](https://read01.com/zh-tw/dxO3gQ.html)
[Exit Codes - Shell Scripting Tutorial](https://www.shellscript.sh/exitcodes.html)
