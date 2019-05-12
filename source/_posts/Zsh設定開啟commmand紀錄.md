---
title: Zsh設定開啟commmand紀錄
date: 2019-05-12 01:22:16
tags: [zsh]
categories: 實用工具
---

最近重裝 zsh
就發現沒有 history 查詢功能(往上)
只要登出，就沒有這個很不方便
今天特別 google 一下

<!--more-->

只要在`~/.zshrc`最上面加

```
HISTSIZE=1000
SAVEHIST=1000
HISTFILE=~/.history
```

存檔就可以完成設定

參考:
[A User's Guide to the Z-Shell](http://zsh.sourceforge.net/Guide/zshguide02.html)
[Command history in Zsh - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/111718/command-history-in-zsh)
