---
layout: post
title: '終端機錄制工具 asciinema'
date: 2018-03-25 11:39
comments: true
categories: 實用工具
tags: asciinema
---
最近想記錄我在筆電上面的終端機設定
但以經放著多天，最近在最有空的時候準備整理一下
本來想用圖片整理，但logdown不能上傳圖片
未來會考慮用hexo來用...

不過在用hexo之後，剛好看到asciinema可以錄制command指令
想說先用這個東西來試試

<!--more-->

官方網站有介紹[Getting started - asciinema](https://asciinema.org/docs/getting-started)安裝步驟
不過...試過`sudo apt-get install asciinema`
在錄制的時候在upload會說版本太舊...

想說用pip3應該就不會有問題
沒想到我電腦沒有pip3
```
#安裝pip3
sudo apt-get install python3-pip
#安裝pip
sudo apt-get install python-pip
```

沒想到在做`sudo pip3 install asciinema`
竟然跳出pip版本問題...
```
pip3 install --upgrade pip
```

再執行
```
sudo pip3 install asciinema
```
可以順利安裝

## asciinema rec [filename]
錄制開始，結束會問要不要上傳
## asciinema play [filename]
播放
##  asciinema cat [filename]
查看顯示結果
## asciinema  upload  [filename]
上傳到asciinema網站上面

錄制結果如下
[![asciicast](https://asciinema.org/a/kLj4j7cwjwsUnrIvTM3GZ9VtC.png)](https://asciinema.org/a/kLj4j7cwjwsUnrIvTM3GZ9VtC)

參考來源：
[升级pip3的正确姿势 - 简书](https://www.jianshu.com/p/34235f5d484a)