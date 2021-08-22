---
title: glob 搜尋匹配規則
date: 2021-08-22 15:28:00
tags: [linux,bash,glob]
categories: Linux
---


常常看到一堆人搜尋會用到`**/*.php`(ex: ./gitignore)，這看起來不向正規化，最近再用 VSCODE 再找類似正規畫套用查詢，結果發現 vscode 查詢適用 glob 。
[No regular expression support for files to include in search. · Issue #36882 · microsoft/vscode](https://github.com/Microsoft/vscode/issues/36882)

<!-- more-->

## 網路上教學

[Gulp 學習筆記 - Glob 篇 | 格物致知](https://amobiz.github.io/2015/11/14/gulp-glob/)

感覺是大坑，裡面還滿詳細的，有用到進階在學。




## 排除方法

~~沒有找到在前面排除方法~~，但有找到在中間排除。但我看這種方法還是少用，像是 VSCode 有排除選項可以選。

`/src/**/!(els)/*.scss`
參考:[Any way to ignore/exclude a file or folder? · Issue #24 · mysticatea/cpx](https://github.com/mysticatea/cpx/issues/24)

後來發現可以做到，`/**/!(xxx.js)`要這樣設定才有效，這樣`!(/**/xxx.js)`沒效
![](https://i.imgur.com/L3HSjIx.png)


[Glob online tester](https://codepen.io/mrmlnc/pen/OXQjrZ)

## globstar

Linux 想說怎麼沒法用，原來預設關閉。

- [Globstar: New Bash Globbing Option | Linux Journal](https://www.linuxjournal.com/content/globstar-new-bash-globbing-option)
- [Java 语言 Glob 语法规则](https://busy.im/post/java-glob-syntax/)

感覺能跟這個搭配[Remove a Specific Element from an Array in Bash – Linux Hint](https://linuxhint.com/remove-specific-array-element-bash/)，可以做到 exlcude 功能。

## 模式匹配

>    ?(pattern-list)：匹配0個或1個pattern
>    *(pattern-list)：匹配0個或以上的patterns
>    +(pattern-list)：匹配1個或以上的patterns
>    @(pattern-list)：只匹配1個pattern
>    !(pattern-list)：匹配任何與pattern不一樣的patterns

其實看中文敘述不是很懂，但實作後發現就懂他的匹配是什麼意思，直接範例

參考:[阿就操場啊~: 延伸檔名匹配 Extended Pattern Matching](https://2formosa.blogspot.com/2019/04/extended-pattern-matching-pathname-expansion.html)

### ?(ab|def)?(.jpg|.gif)

?(pattern-list)：匹配0個或1個pattern

![](https://i.imgur.com/ZKTYsHh.png)

[Glob Tool | DigitalOcean](https://www.digitalocean.com/community/tools/glob?comments=true&glob=%3F%28ab%7Cdef%29%3F%28.jpg%7C.gif%29&matches=false&tests=abdef.jpg&tests=ab.jpg&tests=def.jpg&tests=xxx.jpg)

### \*(ab|def)\*(.jpg|.gif)

`*(ab|def)*(.jpg|.gif)`
*(pattern-list)：匹配0個或以上的patterns

![](https://i.imgur.com/yzyI6oY.png)

[Glob Tool | DigitalOcean](https://www.digitalocean.com/community/tools/glob?comments=true&glob=%2A%28ab%7Cdef%29%2A%28.jpg%7C.gif%29&matches=false&tests=abdef.jpg&tests=ab.jpg&tests=def.jpg&tests=xxx.jpg)

我這邊還不太了解為什麼 xxx.jpg 沒有被抓到，`匹配0個或以上`都可以抓到，那不是應該都能抓到嗎??但我猜測所有條件下還是要抓到一個，就能顯示。

### +(ab|def)+(.jpg|.gif)

+(pattern-list)：匹配1個或以上的patterns

![](https://i.imgur.com/kSPHN3w.png)


### @(ab|def)@(.jpg|.gif)

@(pattern-list)：只匹配1個pattern

![](https://i.imgur.com/TbTtK5l.png)



### !(ab|def)!(.jpg|.gif)


!(pattern-list)：匹配任何與pattern不一樣的patterns


![](https://i.imgur.com/3ChHUTx.png)

### 不知道為什麼.開頭抓不到?

![](https://i.imgur.com/BCdUgYp.png)



這邊就不深入了


### 小記

一般我們使用 `()*+.` 很容易連想正規表示式，但是其實是不一樣的，看到`()`前面*號，會把他想成 regex {0,無限}，但其實是不一樣的。原範例是`*(ab|def)**(.jpg|.gif)`，你會看到`**`，但是其實就是* 第一個 extglob，第二個是模式匹配，簡單來說誇號前面符號要注意。


