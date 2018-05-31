---
title: PHP_xlsx_writer  excel 15位數會進動進位的問題
date: 2018-05-31 21:11:33
tags: [xlsx,excel,PHP]
categories: PHP
---

最近公司用xlsx_writer做報表[mk-j/PHP_XLSXWriter: Lightwight XLSX Excel Spreadsheet Writer in PHP](https://github.com/mk-j/PHP_XLSXWriter)
發現使用者編號(uid)竟然跟DB不一樣....
讓我震驚了!!!以為我程式出BUG了...

<!--more-->

原本以為是我程式哪裡出問題，結果發現var_dump正常
打開Excel就不正常了


15位數Excel會自動補0
[如何解決EXCEL數字過長自動加0的問題？ @ 阿水資訊站 :: 痞客邦 ::](http://waterisme.pixnet.net/blog/post/33761621-%E5%A6%82%E4%BD%95%E8%A7%A3%E6%B1%BAexcel%E6%95%B8%E5%AD%97%E9%81%8E%E9%95%B7%E8%87%AA%E5%8B%95%E5%8A%A00%E7%9A%84%E5%95%8F%E9%A1%8C%EF%BC%9F)

沒想到採到這個雷.....

明明有對header做string
但超過15位數就不正常
其他有設定string(沒超過15位數)都顯示正常


不過最後神同事用`="123456789123456789123456789"`
解決問題!!


