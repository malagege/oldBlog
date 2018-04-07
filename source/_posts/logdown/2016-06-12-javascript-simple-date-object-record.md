---
layout: post
title: '[JavaScript] Date物件  小筆記'
date: 2016-06-12 13:58
comments: true
categories: JavaScript
tags: [JavaScript,Date]
---
最近有使用JavaScript Date物件，想說有什麼辦法直接把變數換成當月最後一天
發現網路上有一個教法寫
```javascript javascript
var date = new Date();
date.setMonth(date.getMonth+1);
date.setDate(0);
console.log(date);
```

非常神奇，簡單又不需要判斷閏年的問題!!!!
<!--more-->



由於同學最近問我有沒有辦法固定網頁的時間先自動調整
我就簡單看一下，在瀏覽器的GreaseMonkey的腳本來寫JavaScript
就用了一下Date物件
```js
var input = jQuery('<input value="06">').change(function(){
	var inputMonth = jQuery(this).val();
	if(inputMonth.length == 1) inputMonth = '0'+ inputMonth;
	var setDate = new Date(2016,inputMonth,1);
	setDate.setMonth(setDate.getMonth());
	setDate.setDate(0);
	var day = setDate.getDate();

	jQuery('[name=**a**]').val('2016-' + inputMonth + '-01 00:00');
	jQuery('[name=**b**]').val('2016-' + inputMonth + '-' + day + ' 23:59');
}).change();

jQuery('#**a**').before(input);
```

順便也把他覺得麻煩的readonly拿掉了XD
~~他以後橫行無阻(誤)~~

這個感覺還滿實用的，先簡單記個筆記
怕太久沒用就忘了XD

為什麼setDate(0)是抓前一個月最後一天呢??
我有試著查看MDN的文件，但好像沒特別說setDate(0)
只看到setDate(32)會是下個月1日，然後又看不太懂ECMASCRIPT文件

最後有在w3cschool看到
```
Required. An integer representing the day of a month.

Expected values are 1-31, but other values are allowed:

    0 will result in the last day of the previous month
    -1 will result in the day before the last day of the previous month

If the month has 31 days:

    32 will result in the first day of the next month

If the month has 30 days:

    32 will result in the second day of the next month

```

比較注意的是呼叫date物件`new Date(year,month-1,day)`，一月month參數為`0`


參考來源：
http://www.w3schools.com/jsref/jsref_setdate.asp
http://www.eion.com.tw/Blogger/?Pid=1148

看到有趣應用
http://stackoverflow.com/questions/13745742/setdate-to-the-date-whose-day-is-zero-that-is-sunday
http://jsfiddle.net/phZr7/1/