---
title: elementary OS更換Terminal字形 & 設定zsh環境變數
date: 2018-08-26 23:17:22
tags:  [Linux]
categories: Linux
---


To change font in the Terminal you need dconf-editor. Install it, if you haven't done so:

sudo apt-get install dconf-tools

Run it and go to path org > gnome > desktop > interface > monospace-font-name. 

[FB](https://www.facebook.com/groups/199493136812961/permalink/1544001102362151/?__xts__%5B0%5D=68.ARC1KrGOX9zJkDFN4oR8J3H5qUpRcg4qwmQqIuL9msps-za6SyHMsgZJBkwg5PyLQVaSV5S9sIzyG5WI982VuimrBiaxzKkLE88JdlPNUY1UQSfEt2d1yeNj2_rXMCr2zrN8ODo&__tn__=-R)
[themes - How can I change the default Terminal font? - elementary OS Stack Exchange](https://elementaryos.stackexchange.com/questions/1149/how-can-i-change-the-default-terminal-font/1153#1153)


.zshrc


```
    下面修改环境变量，将~/.composer/vendor/bin写入 PATH 环境变量里。

    注意： 如果你的命令行安装了zsh和oh-my-zsh，修改环境变量时有些小的变化。

    我遇到的就是这个问题，怎么修改，环境变量都不生效，比如我修改的是~/.bash_profile，那么必须`source
    ~/.bash_profile`才能生效，而且重启后继续无效，为什么呢？因为 zsh ！我的终端是
    iTerm2，它很炫酷，但是就是环境变量搞不成。Google 找到了问题所在，安装 zsh
    后，~/.bash_profile并没有被执行，所以需要编辑 zsh 的配置文件~/.zshrc，在该文件里加入

    source ~/.bash_profile 现在我们应该可以用homestead命令了。

```
https://segmentfault.com/q/1010000004376181/a-1020000004377872


發現這篇很久沒補
就先寫重點吧...
