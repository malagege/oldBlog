---
layout: post
title: 'Markdown新手小試'
date: 2014-06-05 13:17
comments: true
categories: Markdown
tags: Markdown
---
在前幾年有Markdown這個東西，有嘗試著練習。不過...太久沒用了，也沒有實戰用到…，所以全部忘光光囧，但最近看到LogDown這個玩意，就突然想寫寫看。
LogDown是台灣xdite大大做的，也算是台灣之光，所以也來支持一下^^
<!--more-->


#標題寫法
```
標題寫法(最高階)
===
```
or
```
標題寫法(第二階)
---
```
or
```
#標題寫法
```


>引用

```
>引用
```

>引用
>>區塊引用

```
>引用
>>區塊引用
```

+ 清單
```
+ 清單
```
or
```
- 清單
```
or
```
* 清單
```

1. PHP
2. Python
3. Ruby on Rails

```
1. PHP
2. Python
3. Ruby on Rails
```

*   This is a list item with two paragraphs.

    This is the second paragraph in the list item. You're
only required to indent the first line. Lorem ipsum dolor
sit amet, consectetuer adipiscing elit.

*   Another item in the same list.

```
*   This is a list item with two paragraphs.

    This is the second paragraph in the list item. You're (前面空白可縮白)
only required to indent the first line. Lorem ipsum dolor
sit amet, consectetuer adipiscing elit.

*   Another item in the same list.
```

* test

    >test

```
* test

    >test(前面四個空白)
```

1986\.  What a great season.

```
1986\. What a great season.
```

This is a [Google Url ](http://google.com.tw).
```
This is a [Google Url ](http://google.com.tw).
```

This is a 相對位置 [url](test).
This is a  絕對位置 [url](/test/).
```
This is a 相對位置 [url](test).
This is a  絕對位置 [url](/test/).
```

This is a [Google URL][Google].
This is a [谷歌][谷歌].(id:中文也可以)
[Google]: http://google.com.tw  "Google"
[谷歌]: http://google.com.tw  "谷歌"

```
This is a \[Google URL\]\[Google\].
This is a \[Google URL\]\[google]\.
This is a \[谷歌]\[谷歌].(id:中文也可以，不區分大小寫)
\[Google]: http://google.com.tw  "Google"
\[谷歌]: http://google.com.tw  "谷歌"
```

連結Google可以省略寫成
[Google][]
[Google]:http://google.com.tw
```
\[Google][]
\[Google]:http://google.com.tw
```




```
*em*
__strong__
```

`ls -l`(一行顯示)
```
`ls -l`
```


This is a ![Chrome Image](https://www.google.com.tw/images/icons/product/chrome-48.png)(用法跟一般連結差不多)
```
This is a ![Chrome Image](https://www.google.com.tw/images/icons/product/chrome-48.png)
```


單一\*或\_包圍字串會轉成\<em>
兩個\*或\_包圍字串\<strong>
*em*
__strong__
~~strike~~

```
*em*
__strong__
~~strike~~
```

{% blockquote Author Name %}
Flying is learning how to throw yourself at the ground and miss.
{% endblockquote %}

{% blockquote Author Name https://example.com/quotation/source/link %}
Over the past 24 hours I've been reflecting on my life & I've realized only one thing. I need a medieval battle axe.
{% endblockquote %}
```
{% blockquote Author Name %}
Flying is learning how to throw yourself at the ground and miss.
{% endblockquote %}

 \{% blockquote Author Name https://example.com/quotation/source/link %}
Over the past 24 hours I've been reflecting on my life & I've realized only one thing. I need a medieval battle axe.
{% endblockquote %}
```
|第一欄|第二欄|第三欄|
-------|:-------:|:--------:|
|第一格|第二格|第三格|
|第一格|第二格|第三格|

```
|第一欄|第二欄|第三欄|
-------|:-------:|:--------:|
|第一格|第二格|第三格|
|第一格|第二格|第三格|
```
***test***

```mathjax
1+1=2
```


有些用Markdown包起\```來好像怪怪的，不知道是不是我寫錯的關係。以前也不知道Markdown也能寫表格XD
希望以後這些我前用的到。

