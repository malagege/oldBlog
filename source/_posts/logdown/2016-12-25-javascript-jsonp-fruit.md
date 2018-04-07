---
layout: post
title: '[JavaScript] JSONP 小實作'
date: 2016-12-25 16:08
comments: true
categories: JavaScript
tags: [JSONP,JavaScript]
---
很久之前，有看過JSONP文章，雖然有看一下實作原理都懂，但沒有動手做實作
前幾天看到有人討論JSONP不能用POST方法去做
這就讓我非常好奇是怎麼一回事
<!--more-->

首先，
先從網路找一份PHP做JSONP範例
```testJsonp.php
  <?php
 $fname = $_GET['firstname'];
         echo $_GET['callback'] . '(' . "{'fullname' : 'Jeff Hansen'}" . ')';
?>
```

前端就先用jQuery AJAX實作吧
只不過...我真的不太熟
一開始就碰壁，不多說馬上筆記

我很懶，在jQuery官網在開發工具做測試
只不過...一直沒成功，讓我好懊腦

看開發工具好像寫https關係
https://developer.mozilla.org/zh-TW/docs/Security/MixedContent?utm_campaign=default&utm_medium=firefox-console-errors&utm_source=mozilla

在http就正常了
```js
    $.ajax({
         type: "get",
         url: "http://10.10.1.132/testJsonp.php?callback=?&firstname=Jefssf",
         dataType: "jsonp",
         //json: 'callback',
         //jsonpCallback: 'abc',//"mycallback"變數的值，沒指定時，jQuery隨機產生
         success: function(res){
             console.log(res);
         },
         error: function(){
             alert('fail');
         }
});
```
`http://10.10.1.132/testJsonp.php?callback=jQuery1113042020808678791977_1482680736926&firstname=Jefssf&_=1482680736939`
上面網址callback沒有指定的話，產生出來自動為亂數
ajax裡的參數 jsonp為 `xxx`=? 參數命名
jsonpCallback=傳回來function命名 ，xxx=`?`參數，沒指定callback產生出來自動為亂數


至於JSONP為什麼不能用POST，下次再做探討。


相關資料
JSONP跨網域傳送檔案：以POST方法實作 - 布丁布丁吃什麼？
http://blog.pulipuli.info/2011/05/jsonppost.html
Simple jQuery, PHP and JSONP example? - Stack Overflow
http://stackoverflow.com/questions/6809053/simple-jquery-php-and-jsonp-example
jQuery.ajax() | jQuery API Documentation
http://api.jquery.com/jQuery.ajax/
success_jsonpCallback
http://www.cnblogs.com/know/archive/2011/10/09/2204005.html
Working with JSONP | jQuery Learning Center
http://learn.jquery.com/ajax/working-with-jsonp/
混和內容 - Security | MDN
https://developer.mozilla.org/zh-TW/docs/Security/MixedContent?utm_campaign=default&utm_medium=firefox-console-errors&utm_source=mozilla
cross
http://www.css88.com/jqapi-1.9/jQuery.ajax/
Attribute Ends With Selector [name$="value"] | jQuery API中文文档(适用jQuery 1.0 - jQuery 3.0)
http://www.css88.com/jqapi-1.9/attribute-ends-with-selector/
使用 JSONP 跨站請求
http://openhome.cc/Gossip/JavaScript/JSONP.html