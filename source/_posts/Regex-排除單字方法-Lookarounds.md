---
title: Regex 排除單字方法(Lookarounds)
date: 2018-07-03 21:07:23
tags: [regex,lookarounds]
categories: Regex
---

這次專案有用到email學生任證，有想一個用email方法
但這次需求要把一些教育信箱排除在外...
查了一下regex好像沒法對特定單字做
不過我有實做一個方法(不知道有沒有漏洞)

<!--more-->

### a(?!xxx)
不會抓axxx，但會抓ax,axx,aoxxx的`a`

### a(?=xxx)
會抓axxx的`a`

###  (?:xxx)
不會算group

最後再沒有找到資料情況下，有例用上面3個想到一個方法

`@(?!nfu)\S*edu.tw`

例用`(?!xxx)`跟`\S`(非空白字符)
真的太久沒有寫會忘orz
我每次都是寫的時候再查

```
\s 元字符用于查找空白字符。

空白字符可以是：

    空格符 (space character)
    制表符 (tab character)
    回车符 (carriage return character)
    换行符 (new line character)
    垂直换行符 (vertical tab character)
    换页符 (form feed character)
```
http://www.w3school.com.cn/js/jsref_regexp_whitespace.asp


[regex - Regular expression to match a line that doesn't contain a word? - Stack Overflow](https://stackoverflow.com/questions/406230/regular-expression-to-match-a-line-that-doesnt-contain-a-word)
還有看到這篇，但好像不實用



[Regex Cheat Sheet](http://www.rexegg.com/regex-quickstart.html#lookarounds)看到裡面還有很多不會
感覺還有更深的東西。有時間再食用。


參考來源：
* [regex – 排除单词/字符串的正则表达式 - 代码日志](https://codeday.me/bug/20170511/14935.html)
* [regex - Regular expression to match a line that doesn't contain a word? - Stack Overflow](https://stackoverflow.com/questions/406230/regular-expression-to-match-a-line-that-doesnt-contain-a-word)
* [Match string not containing string - Regex Tester/Debugger](https://www.regextester.com/15)
* [正则的扩展 - ECMAScript 6入门](http://es6.ruanyifeng.com/#docs/regex)
