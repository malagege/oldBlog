---
title: Git 中文顯示亂碼
date: 2022-02-13 23:51:01
tags: [git]
categories: Git 中文顯示亂碼
---


之前使用 Window 10 cmd 用 Git 指令會看中文會有亂碼，一直使用 bash都不會有這個問題，最近查了一下要怎麼處理，順便記錄一下。

<!--more-->

## 參考來源



### [GIT - 中文亂碼的問題 | YuShu Hsiao - 點部落](https://dotblogs.com.tw/H20/2018/06/22/111411)

```bash=
git config --global core.quotepath false
```

![](https://i.imgur.com/fnb4q3m.png)


cmd 下 git log 還是會看到亂碼，但是bash 不會看到亂碼，可用下面指令解決
```
set LC_ALL=C.UTF-8
```


win 10 可以用下面指令永久設定
```
setx LC_ALL C.UTF-8
```


### [GIT gitk图形界面中文乱码修改_kongxingxing的专栏-CSDN博客](https://blog.csdn.net/kongxingxing/article/details/77993095)


```
git config --global gui.encoding utf-8

```