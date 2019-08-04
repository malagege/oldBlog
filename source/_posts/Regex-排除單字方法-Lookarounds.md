---
title: Regex 排除單字方法(Lookarounds)
date: 2018-07-03 21:07:23
tags: [regex, lookarounds]
categories: Regex
---

這次專案有用到 email 學生任證，有想一個用 email 方法
但這次需求要把一些教育信箱排除在外...
查了一下 regex 好像沒法對特定單字做
不過我有實做一個方法(不知道有沒有漏洞)

<!--more-->

### a(?!xxx)

不會抓 axxx，但會抓 ax,axx,aoxxx 的`a`

### a(?=xxx)

會抓 axxx 的`a`

### (?:xxx)

不會算 group

最後再沒有找到資料情況下，有例用上面 3 個想到一個方法

`@(?!nfu)\S*edu.tw`

例用`(?!xxx)`跟`\S`(非空白字符)
真的太久沒有寫會忘 orz
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

**2019-07-25**

今天在改 PHP 程式，由於要改一些程式把`xxx_line`去除掉
但是跟 PHP `__LINE__`衝突
不過後來找到正規化去除`(?<!_)_line`

[無聊技術研究: RegExp 應用： lookahead , lookbehind](http://darkk6.blogspot.com/2017/03/regexp-lookahead-lookbehind.html) {% asset_link web1.png 備份圖 %}

裡面有提到`lookahead`(往前看)

    Positive lookahead ： a(?=Y)
    Negative lookahead ： a(?!Y)

剛我這個例子是用`lookbehind`(往後看)
之前沒有記錄到

Positive lookbehind ： (?<=Y)a
Negative lookbehind： (?<!Y)a

參考來源：

- [regex – 排除单词/字符串的正则表达式 - 代码日志](https://codeday.me/bug/20170511/14935.html)
- [regex - Regular expression to match a line that doesn't contain a word? - Stack Overflow](https://stackoverflow.com/questions/406230/regular-expression-to-match-a-line-that-doesnt-contain-a-word)
- [Match string not containing string - Regex Tester/Debugger](https://www.regextester.com/15)
- [正则的扩展 - ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/regex)
