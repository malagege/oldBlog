---
layout: post
title: '[CSS]文字換行的問題'
date: 2016-06-19 08:18
comments: true
categories: CSS
tags: [word-break,word-wrap]
---
由於最近做一個報表系統，在測試環境下沒有很長的資料，報表表格都沒有什麼問題
但在正試環境，很多文字就有自動換行的問題，然後爬了文但不清楚這些東西
花了一個下午時間理解這些東西，順便記一下筆記，不然很久沒用應該就忘掉了 囧

word-break:normal|break-all|keep-all;
word-wrap:normal|break-word

上面都是解決換行的問題(但不是真的換行)

word-break:Normal
-----------------------
<div style="border: 1px solid ; width:100px;height:200px;">
I am bone of my code. I am bone of my code. I am bone of my code.  I am bone of my codecodecodecodecode.
</div>
一般 word-break,word-wrap;都是normal
這些是截斷英文單字有關係，不是指整行是否段行

*normal下一句有單字超過邊距會把整個單字換下一行*

word-break:keep-all;
-----------------------
<div style="border: 1px solid ; width:100px;height:200px;word-break:keep-all;">
I am bone of my code. I am bone of my code. I am bone of my code.  I am bone of my codecodecodecodecode.
</div>

恩，結果好像跟Normal模式一樣，沒什麼差別...，但這樣就特錯大錯
查了一下文件如下
```
normal
    Use the default line break rule.
break-all
    Word breaks may be inserted between any character for non-CJK (Chinese/Japanese/Korean) text.
keep-all
    Don't allow word breaks for CJK text. Non-CJK text behavior is the same as for normal.
```
http://devdocs.io/css/word-break

官網下面範例就很清楚`keep-all`英文下還是以`normal`，但非英文(中文、日文...)不會換行
這邊我把他想像成   會把一串中文變成一整個單字，所以換行
<div style="border: 1px solid ; width:100px;height:300px;">
I am bone of my code. I am bone of my code. I am bone of my code.  I am bone of my codecodecodecodecode.
此身為碼而生. 此身為碼而生. 此身為碼而生. 此身為碼碼碼碼而生.
</div>
一般 word-break,word-wrap;都是`normal`
這些是截斷英文單字有關係，不是指整行是否段行
word-break:keep-all;
<div style="border: 1px solid ; width:100px;height:300px;word-break:keep-all;">
I am bone of my code. I am bone of my code. I am bone of my code.  I am bone of my codecodecodecodecode.
此身為碼而生. 此身為碼而生. 此身為碼而生. 此身為碼碼碼碼而生.
</div>

**剛發現中文與中文之間有空白還是會做換行動作**

好了，接下來要講word-break:break-all;

word-break:break-all
------------------------
<div style="border: 1px solid ; width:100px;height:300px;word-break:break-all;">
I am bone of my code. I am bone of my code. I am bone of my code.  I am bone of my codecodecodecodecode.
此身為碼而生. 此身為碼而生. 此身為碼而生. 此身為碼碼碼碼而生.
</div>
以下結果我們看出英文超出單字不會自動換行


white-space:nowrap;
一段不會換行的問題

<div style="white-space:nowrap;">
I am bone of my code. I am bone of my code. I am bone of my code.  I am bone of my codecodecodecodecode.
此身為碼而生. 此身為碼而生. 此身為碼而生. 此身為碼碼碼碼而生.
</div>

我的table不會亂用`word-break:keep-all;`或`white-space:nowrap;`可以解決問題


測試還發現`display:block`裡面文字超出去
但是外框大小不會跟著文字延伸
`display:inline-block`就沒有這個問題
上面的div文字會超出去邊框，但table沒有這個問題
看來有空要搞清楚這些....

