---
title: xargs 參數代換簡單使用筆記
date: 2018-08-18 13:19:03
tags: [linux,xargs]
categories: Linux
---


最近做`find . -mmin -60 |  ls -l `發現怎麼不能讀ls內容
原來我以為 `|`pipe 就是把輸出結果加到後面去
但是並非跟`<,<<`一樣
所以我在stackoverflow找到[linux - Unix pipe into ls - Stack Overflow](https://stackoverflow.com/questions/18210956/unix-pipe-into-ls)
要使用xargs
不過我真的對這個不太熟



<!--more-->

相關資料流重新導向可以看[鳥哥的 Linux 私房菜 -- 第十章、認識與學習BASH](http://linux.vbird.org/linux_basic/0320bash.php#redirect)
{% asset_link Wang_web.png "上面連結備份圖" %}

xargs網路有爬到[Linux 系統 xargs 指令範例與教學 - G. T. Wang](https://blog.gtwang.org/linux/xargs-command-examples-in-linux-unix/)講解非常清楚

這邊我就簡單紀錄看起來很常用的指令

`xargs` 在指令輸入
可以接著輸入很多東西
```
a b c
def
```
再按`Ctrl+d`可以看到螢幕輸出`a b c def`

## xargs簡單小試

` xargs ls`
再輸入`.`
再按下`Ctrl+D`
可以跳出

所以我們`find . -mmin -60 |  ls -l `這段
要改成`find . -mmin -60 | xargs  ls -l`

## 執行前的確認
`-p`會問要不要執行這個參數
echo a b c d e f| xargs -p



## 實用指令

參考[Linux 系統 xargs 指令範例與教學 - G. T. Wang](https://blog.gtwang.org/linux/xargs-command-examples-in-linux-unix/)
```sh
find . -name '*.c' | xargs grep 'stdlib.h'
```
輸出結果
```
./CMake/TestOggTheoraSubsampling.c:#include <stdlib.h>
./Wrapping/Tools/lex.yy.c:#include <stdlib.h>
./Wrapping/Tools/vtkWrapPythonInit.c:#include <stdlib.h>
./Wrapping/Tools/vtkWrapTcl.c:#include <stdlib.h>
./Wrapping/Tools/vtkWrapTclInit.c:#include <stdlib.h>
./Wrapping/Tools/vtkParsePreprocess.c:#include <stdlib.h>
./Wrapping/Tools/vtkWrapText.c:#include <stdlib.h>
./Wrapping/Tools/vtkParseData.c:#include <stdlib.h>
./Wrapping/Tools/vtkParseHierarchy.c:#include <stdlib.h>
./Wrapping/Tools/vtkParseMain.c:#include <stdlib.h>
```

這個指令感覺很實用