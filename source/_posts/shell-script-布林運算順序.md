---
title: shell script 布林運算順序
date: 2019-11-06 23:01:59
tags: [shell]
categories: Linux
---

一般寫程式都有 not and or 順序去做運算
但今天我發現 shell script 運算結果跟(別的)程式結果不太一樣
查了一下發現，shell script 是由`左往右`

<!--more-->

這邊就很清楚，說shell 布林運算都是**左到右**

> In many computer languages, operators with the same precedence are left-associative. That is, in the absence of grouping structures, leftmost operations are executed first. Bash is no exception to this rule.
> This is important because, in Bash, && and || have the same precedence.
> So what happens in your example is that the leftmost operation (||) is carried out first:
> ...
[bash - Precedence of the shell logical operators &&, || - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/88850/precedence-of-the-shell-logical-operators)


## if 多條件 boolean 寫法

可用 && , ||

```sh
if [ -e $xxxx ] || [ -e $oooo ] && [ -e $dddd ] && [  -e $eeee ]
then
    ....
else
    ....
fi
```

可用 bash `[[` 和 `]]`調整順序
```bash
if [ -e $dddd ] && [  -e $eeee ] && [[ -e $xxxx  ||  -e $oooo ]] 
then
    ....
else
    ....
fi
```

發現 sh 也可以用 `(` `)`
```sh
if [ -e $dddd ] && [  -e $eeee ] && ([ -e $xxxx ] || [ -e $oooo ]) 
then
    ....
else
    ....
fi

```

還可以這樣用`-o` `-a`

```sh
if [ -e $dddd ] && [  -e $eeee ] && [ -e $xxxx -o -e $oooo ]
then
    ....
else
    ....
fi

```

參考連結:

[Bash if statement with multiple conditions throws an error - Stack Overflow](https://stackoverflow.com/questions/16203088/bash-if-statement-with-multiple-conditions-throws-an-error)
[Multiple conditions in if statement shell script - Stack Overflow](https://stackoverflow.com/questions/23542574/multiple-conditions-in-if-statement-shell-script)
[shell - Boolean operators ( &&, -a, ||, -o ) in Bash - Stack Overflow](https://stackoverflow.com/questions/20449680/boolean-operators-a-o-in-bash)
[bash - How to represent multiple conditions in a shell if statement? - Stack Overflow](https://stackoverflow.com/questions/3826425/how-to-represent-multiple-conditions-in-a-shell-if-statement)

## 彩蛋

[Vagrant介绍-从使用到放弃再到掌握完全指南 - 宋净超的博客|Cloud Native|云原生布道师](https://jimmysong.io/posts/vagrant-intro/)
[bash - How do you break an array in groups of n - Stack Overflow](https://stackoverflow.com/questions/23747612/how-do-you-break-an-array-in-groups-of-n)

比較檔案是否一致
[Fastest way to tell if two files are the same in Unix/Linux? - Stack Overflow](https://stackoverflow.com/questions/12900538/fastest-way-to-tell-if-two-files-are-the-same-in-unix-linux)