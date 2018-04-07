---
layout: post
title: 'JQuery相關Animate function筆記'
date: 2014-07-09 21:47
comments: true
categories: 網頁特效
tags: [animate]
---
最近發現JQuery 有人用Animate做背景動態效果
```javascript
$('body').stop().animate({backgroundColor:'#00b1ff'},500);
```
自已用plunker測試發現不能使用
原來需要裝一個套件才能用
<!--more-->

> All animated properties should be animated to a single numeric value, except as noted below; most properties that are non-numeric cannot be animated using basic jQuery functionality (For example, width, height, or left can be animated but background-color cannot be, unless the jQuery.Color() plugin is used). Property values are treated as a number of pixels unless otherwise specified. The units em and % can be specified where applicable.

[jquery-color套件](https://github.com/jquery/jquery-color)
套件能用Animate套用CSS屬性如下
> Supported properties
------------------------------
backgroundColor, borderBottomColor, borderLeftColor, borderRightColor, borderTopColor, color, columnRuleColor, outlineColor, textDecorationColor, textEmphasisColor


Stop()
---------
Stop()能暫停animate動作

delay()
--------
延遲animate動作時間

簡單滑入滑出動作範例

```javascript
$("h1").hover(function(){
	$(this).fadeTo(0, 0.7);
	},function(){
		$(this).fadeTo(0, 1.0);
  });
```


