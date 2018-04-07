---
layout: post
title: '超級終端機 指令補全'
date: 2018-01-22 02:45
comments: true
categories: Linux
tags: [zplug,Linux,zsh]
---
oh-my-zsh有一個指令補全功能
再我裝zsh在elementary OS 上面少這個功能
就不太習慣XD

好來我找到了

在[oh-my-zsh/completion.zsh at master · robbyrussell/oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh/blob/master/lib/completion.zsh)抓下來(檔案抓下來，對照設定檔錄徑)
.zshrc設定
```
#　TAB 補完選單
source ~/.zplug/completion.zsh
```
就能完成了

![asciicast](https://asciinema.org/a/kLj4j7cwjwsUnrIvTM3GZ9VtC.png)
[URL](https://asciinema.org/a/kLj4j7cwjwsUnrIvTM3GZ9VtC)

[oh-my-zsh/completion.zsh at master · robbyrussell/oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh/blob/master/lib/completion.zsh)
[Installing zsh-autosuggestions - asciinema](https://asciinema.org/a/37390)


