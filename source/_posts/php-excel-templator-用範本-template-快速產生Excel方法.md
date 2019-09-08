---
title: php-excel-templator 用範本(template)快速產生Excel方法
date: 2019-09-03 19:36:21
tags: [php,template]
categories: [PHP]
---

最近在工作上常常做報表(Excel)
常常用程式控制 Excel 格式
非常不方便
最近在找找有沒有用 template 好用工具

<!--more-->

之前有找這些東西，但沒有比較好用。最近看到`php-excel-templator`這個，完整性感覺還滿高的。

## php-excel-templator

[alhimik1986/php-excel-templator: PHP Spreadsheet extension to the export excel file from excel template](https://github.com/alhimik1986/php-excel-templator)

可以從 array 去塞一維、二維陣列
>     {var_name} - for string values
>    [var_name] - for one-dimensional arrays
>    [[var_name]] - for two-dimensional arrays

雖然這個沒有從 Excel 塞`key`進去，需要照 array 每個值的位子去塞
但還有對相關欄位做背景顏色設定
這部份可相對完整!!
裡面還有範例，可以算是非常好上手


## 其他程式

[optilude/xlsx-template: A NodeJS module to generate Excel files in .xlsx format from a template created with Excel itself](https://github.com/optilude/xlsx-template)
這個星星數也滿高的，有針對 key 去塞
裡面也有範例檔案，但`key`沒對到程式就會出現錯誤

[ivahaev/go-xlsx-templater: Simple xlsx template based document generator](https://github.com/ivahaev/go-xlsx-templater)
[透過MAATWEBSITE/LARAVEL-EXCEL匯出資料-EXPORT - Dustin's murmur](https://dustinhsiao21.com/2018/08/15/maatwebsite-laravel-excel-export/)


## 小記

有網路上爬到 `docx` 其實是一個壓縮檔，可以直接用 zip 打開，看裡面的檔案
難怪之前有一篇有用[Java操作POI修改報表sample檔發生信任活頁簿問題 | 程式狂想筆記](https://malagege.github.io/blog/2018/09/18/Java%E6%93%8D%E4%BD%9CPOI%E4%BF%AE%E6%94%B9%E5%A0%B1%E8%A1%A8sample%E6%AA%94%E7%99%BC%E7%94%9F%E4%BF%A1%E4%BB%BB%E6%B4%BB%E9%A0%81%E7%B0%BF%E5%95%8F%E9%A1%8C/)去看問題


這當中有
[Open XML SDK and Excel generation from template | The ASP.NET Forums](https://forums.asp.net/t/2119763.aspx?Open+XML+SDK+and+Excel+generation+from+template)


之前有寫過類似文章
[Word,Excel的相關template套件小記 | 程式狂想筆記](https://malagege.github.io/blog/2019/05/05/Word-Excel%E7%9A%84%E7%9B%B8%E9%97%9Ctemplate%E5%A5%97%E4%BB%B6%E5%B0%8F%E8%A8%98/)



[語言的大雜燴: [NuGet]用OpenXml在docx中套版列印 - 基本型](http://lhzyaminabe.blogspot.com/2016/01/openxmldocx.html)
[【雛型】Docx套版列印功能試作-黑暗執行緒](https://blog.darkthread.net/blog/docx-templating/)


**2019-09-08**
發現[shadz3rg/PHPStamp: The XSL-way templating library for MS Office Word DOCX documents.](https://github.com/shadz3rg/PHPStamp)這個感覺不錯用套件
之前有寫過這篇[Word,Excel的相關template套件小記 | 程式狂想筆記](https://malagege.github.io/blog/2019/05/05/Word-Excel%E7%9A%84%E7%9B%B8%E9%97%9Ctemplate%E5%A5%97%E4%BB%B6%E5%B0%8F%E8%A8%98/)
