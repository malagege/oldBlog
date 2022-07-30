---
title: jSignPdf 簡單做簽章PDF動作
date: 2022-07-30 23:15:14
tags: [java,jSignPdf]
categories: Java
---

jSignPdf 簡單做簽章PDF動作

最近接到一個專案需要做 PDF 簽章，雖然程式有寫好了，但想自己測試簽章是不是能過，這邊有找到一個相關工具，有操作PDF簽章成功，這邊簡單留個紀錄。

<!--more-->

## 執行 pdf

Github:[GitHub - intoolswetrust/jsignpdf: PDF signing software written in Java. It supports visible signatures, timestamping, certificate verification and many other cool features](https://github.com/intoolswetrust/jsignpdf/)
可在官網抓zip(免安裝):[JSignPdf - Browse /stable at SourceForge.net](https://sourceforge.net/projects/jsignpdf/files/stable/)

下載下來解壓縮。

```bash=
java -jar JSignPdf.jar
```

![](https://i.imgur.com/QMvARl5.png)

首先需要有pkcs12檔案(pfx)，它裡面有私鑰、公鑰，通常會用密碼包起來。

1. 選擇 PKCS12 
2. 選擇 pfx(pkcs12) 檔案
3. 輸入 pfx 密碼
4. PDF 選擇不加密
5. 原有檔案增加簽章憑證
6. 選擇加密 SHA1/SHA256 (因為我憑證用SHA1，這邊選SHA1)
7. PDF 顯示簽章位置
8. 按下簽章動作

簽章成功顯示如下圖。

![](https://i.imgur.com/TRrodjA.png)


### 框選簽章位置

選擇下面`Setting`會跳出設定顯示簽章設定。
![](https://i.imgur.com/23y5yby.png)

按下`Preview & Select` 可以顯示簽章位置。
![](https://i.imgur.com/kWCYCnP.png)


## 過期無法簽章

過期憑證無法加簽。

![](https://i.imgur.com/xrxFczz.png)

## 彩蛋

- [GitHub - LibreSign/libresign: ✍️ Nextcloud app to sign PDF documents](https://github.com/LibreSign/libresign)
- [peepdf - PDF Analysis Tool | eternal-todo.com](https://eternal-todo.com/tools/peepdf-pdf-analysis-tool)
- [How To Digitally Sign PDF Documents Using JSignPDF](https://www.ilovefreesoftware.com/09/tutorial/digitally-sign-pdf-documents-using-jsignpdf.html) 
裡面有提到輸入內容，建議可以看一下。
![](https://i.imgur.com/eX6OhqB.png)

- [GitHub - bersch/SignPDF: Signing and protecting PDF documents; Implemented in Java; Uses iText; License: AGPL](https://github.com/bersch/SignPDF)
- [GitHub - ekiametis/pdf-signer: A REST Service to sign PDF documents](https://github.com/ekiametis/pdf-signer)
- [GitHub - vbuch/node-signpdf: Simple signing of PDFs in node.](https://github.com/vbuch/node-signpdf)
- [java - How to sign PDF with a x.509 signature/certificate - Stack Overflow](https://stackoverflow.com/questions/44749907/how-to-sign-pdf-with-a-x-509-signature-certificate)

### 查看 pfx (番外)


```
certutil -dump _pfx_file_
```