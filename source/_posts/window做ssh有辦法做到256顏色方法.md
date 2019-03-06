---
title: window做ssh有辦法做到256顏色方法
date: 2019-01-07 22:31:05
tags: [ssh]
categories: 實用工具
---

最近打算把筆電 elementary os 帶去公司開發
主要想把 docker 環境帶過去
但是我電腦設定 zsh
原本想共用電腦切換
但卡在滑鼠鍵盤不能共用
所以有找一些方法

- USB 切換器 OR KVM (價格也不便宜)
- USB 拷貝線(價格不便宜，但只能用在 Window 上面...)
- [键盘鼠标共享效率工具-Synergy - 简书](https://www.jianshu.com/p/9fd09bb9f694)

原本最後想用 Synergy
但是我又不想使用...網路
最後看到有人說 Linux 還需要遠端嗎? ssh 就好 XDD
我覺得好像有道理
但是一般 pietty 進去 zsh 沒有 256 顏色真的醜到爆...
所以找了一些方法

<!--more-->

網路一堆教 putty 方法，但我測試都不能用...
所以決定放棄

[Help with 256 color support on Windows · Issue #1968 · zeit/hyper](https://github.com/zeit/hyper/issues/1968)
原本看到這篇有安裝 hyper
但沒成功支援 256 色
~~工程師壓力大...看 cmd 黑白會 coding 越會黑白，舒適好看 command 才看得爽~~
但下面有人提到[Terminus](https://eugeny.github.io/terminus/)
立馬下載免安裝
[Release Alpha 68 · Eugeny/terminus](https://github.com/Eugeny/terminus/releases/tag/v1.0.68)
設定字型`hack`
windows 可正常跑出顏色!!!
雖然有些還不是很完美(下面不知道為什麼會多一行)
但至少顏色都出來

{% asset_img 1.jpg %}

其他參考來源:

- [USB 对拷线设置教程](http://www.lulian.cn/news/277-cn.html)
- [win10 下的 VSCode 用 cmder 作默认的命令终端(shell) - 简书](https://www.jianshu.com/p/c3b162df3b57)
- [PowerShell 美化：oh my posh | Flymia 凡事用心之事](https://ppundsh.github.io/posts/ad6e/)
- [Oh My ZSH! with PuTTY » mordsgau.de Blog](https://blog.mordsgau.de/oh-my-zsh-with-putty/)
- [How do you do remote access? : elementaryos](https://www.reddit.com/r/elementaryos/comments/62ifss/how_do_you_do_remote_access/)
- [判断终端色彩支持情况的方法 | 高明飞的博客](http://gaomf.cn/2017/01/16/Terminal_Color/)
- [键盘鼠标共享效率工具-Synergy - 简书](https://www.jianshu.com/p/9fd09bb9f694)
- [Chocolatey Gallery | synergy (Install) 1.7.5](https://chocolatey.org/packages/synergy)
