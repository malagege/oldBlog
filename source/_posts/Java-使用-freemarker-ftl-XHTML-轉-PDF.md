---
title: Java 使用 freemarker (ftl) XHTML 轉 PDF
date: 2021-02-23 20:49:24
tags: [java,freemarker,ftl]
categories: Java
---

TODO: 整理

<!--more-->
* [haopenge/freemark-html-xhtml-pdf: freemark-html-xhtml-pdf ，从名称可以看出此项目是三者之间的转换，用于生成pdf 文件的](https://github.com/haopenge/freemark-html-xhtml-pdf)
* [freemarker生成html、html轉pdf、pdf根據關鍵字定位、pdf簽名 - IT閱讀](https://www.itread01.com/content/1544193481.html)
* [java通过html生成pdf,支持css和图片以及横向打印 - 林宇风 - 博客园](https://www.cnblogs.com/linyufeng/p/13402901.html)
* [java通过html生成pdf,支持css和图片以及横向打印 - 林宇风 - 博客园](https://www.cnblogs.com/linyufeng/p/13402901.html)
* [xhtmlrenderer + iText-HTML转PDF_hunan961的博客-CSDN博客_xhtmlrenderer](https://blog.csdn.net/hunan961/article/details/104901519)
* [itext将html转为pdf,图片标签为base64的处理 - 小翔的个人空间 - OSCHINA - 中文开源技术交流社区](https://my.oschina.net/yifanxiang/blog/680936)
* [itext将html转为pdf,图片标签为base64的处理 - 小翔的个人空间 - OSCHINA - 中文开源技术交流社区](https://my.oschina.net/yifanxiang/blog/680936)


## 中文看不見問題(字型)

* [解决PDF添加水印报错iText： Font 'STSong-Light' with 'UniGB-UCS2-H' is not recognized_HaHa_Sir的博客-CSDN博客](https://blog.csdn.net/HaHa_Sir/article/details/84350671)
* [JWorld@TW Java論壇 - iText RtfShape 內文為中文會出現亂碼](https://www.javaworld.com.tw/jute/post/view?bid=11&id=266144&tpg=1&ppg=1&sty=1&age=0#266144)
* [iText - PsMonkey 隨手筆記](http://wiki.psmonkey.org/java-3rd-lib/itext)
* [Java iText+FreeMarker生成PDF(HTML转PDF) - 云飞扬-88 - 博客园](https://www.cnblogs.com/yunfeiyang-88/p/10984740.html)
* [iText 解決中文問字型問題 顯示中文 | 程式前沿](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/547725/)

簡單說明一下，我這邊使用方法不是載入程式內部字型
是使用 jar 裡面自型解決[解决PDF添加水印报错iText： Font 'STSong-Light' with 'UniGB-UCS2-H' is not recognized_HaHa_Sir的博客-CSDN博客](https://blog.csdn.net/HaHa_Sir/article/details/84350671)

> 1、itext 經歷了從： itext.itext ---> com.lowagie ---> com.itextpdf 的三次升級，若使用了舊版本的jar，就會出現錯誤，還不好發現。
> 2、多從maven repository 搜索下相關jar包，關注組件的歷史變化情況。
> 3、會debeug，會追蹤源碼，找到拋出異常的根本原因所在！
這邊找相對應java 版本，所以把itext-asian升到 5.2 就可以跑了
後來對應網路上字型都不能跑(會亂碼)，
猜測是簡體字型關係，看到[JWorld@TW Java論壇 - iText RtfShape 內文為中文會出現亂碼](https://www.javaworld.com.tw/jute/post/view?bid=11&id=266144&tpg=1&ppg=1&sty=1&age=0#266144)

```java
 bfChinese = BaseFont.createFont("MHei-Medium", "UniCNS-UCS2-H", BaseFont.NOT_EMBEDDED);
``` 

就可以跑出中文字，感動QQ

後來發現用 Android 中文會看不到
可能用標楷體會能解決

## 圖片(base64)

## 轉 xhtml



## 別的方法

[jhonnymertz/java-wkhtmltopdf-wrapper: A Java wrapper for wkhtmltopdf](https://github.com/jhonnymertz/java-wkhtmltopdf-wrapper)
