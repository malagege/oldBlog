---
title: itextpdf 使用 PdfReader 遇到檔案 lock 住問題
date: 2022-07-30 23:57:03
tags: [itextpdf,pdfreader]
categories: Java
---

最近換個環境執行，結果發現檔案刪除會失敗。我還以為是`MultipartFile.transferTo 遇到 FileNotFoundException 問題`，後來發現不是，檔案被 lock 掉。

<!--more-->

但沒找到相關錯誤，我都有做`close`動作。

![](https://i.imgur.com/4Yw1H42.png)


我後來發現這個錯誤訊息。
```java=
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by com.itextpdf.text.io.ByteBufferRandomAccessSource$1 (file:/C:/Users/XXX/.m2/repository/com/itextpdf/itextpdf/5.5.13/itextpdf-5.5.13.jar) to method java.nio.DirectByteBuffer.cleaner()
WARNING: Please consider reporting this to the maintainers of com.itextpdf.text.io.ByteBufferRandomAccessSource$1
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
```

<!--more-->

照相關錯誤找到[java - itext Java 11 : Illegal reflective access by com. itextpdf.io.source.ByteBufferRandomAccessSource$1 - Cache One](https://cache.one/read/6074266)

```java=
new PdfReader(new FileInputStream(inFilePath)
```

補上FileInputStream就可以跑了。~~三小~~
反正採到一個很奇怪的雷。

參考:
- [java - itext Java 11 : Illegal reflective access by com. itextpdf.io.source.ByteBufferRandomAccessSource$1 - Cache One](https://cache.one/read/6074266)
- [Itext Java 11: Illegal reflective access by com.itextpdf.io.source.ByteBufferRandomAccessSource$1 - Stack Overflow](https://stackoverflow.com/questions/53301158/itext-java-11-illegal-reflective-access-by-com-itextpdf-io-source-bytebufferran)