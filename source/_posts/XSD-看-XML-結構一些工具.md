---
title: XSD 看 XML 結構一些工具
date: 2019-09-07 01:16:58
tags: [xml,xsd]
categories: XML
---

最近看 XSD 檔案，覺得這個東西有轉成圖片來看
不過找了一些工具試試

<!--more-->

## XSD 實用工具

### XSDVI

這個可以轉成圖
目前不方便測試這個
功能好像還不錯!!

http://xsdvi.sourceforge.net/ipo.svg


http://sourceforge.net/projects/xsdvi/

git clone 下來
ant 完會在dist
在依照範例執行可以產生 SVG
還滿簡單的

### jEdit XML Plugin

沒測試

### CAM editor 

[CAM Editor](http://camprocessor.sourceforge.net/wiki/index.php/Main_Page)

我 linux 打不開就沒測試了

### XSD Diagram

[Dgis XSD Diagram](http://regis.cosnier.free.fr/?page=XSDDiagram)

不知道為什麼我的 XSD 不能用在上面...

### Eclipse XSD Editor 

可以簡單看 XML 工具

PS安裝小記:
不知道為什麼我用 `apt` 安裝不能打開
使用 `snap` 安裝 eclipse 就正常...

這個能看 XML 格式，算滿中規中矩，介面不太差

參考來源:
[XML Schema editors - Wikipedia](https://en.wikipedia.org/wiki/XML_Schema_editors)

### QXmlEdit
[最簡單實用的QXmlEdit，程式設計師你用了嗎？ - 每日頭條](https://kknews.cc/zh-tw/tech/gqqj39m.html)
[QXmlEdit - simple XML editor](http://qxmledit.org/)

這個終於可以開啟我的 XML 感動
功能還算滿不錯

不知道官網抓下來檔案不能開
所以我用 `snap` 安裝

```bash
sudo apt install snapd
# [Install qxmledit for Linux using the Snap Store | Snapcraft](https://snapcraft.io/qxmledit)
sudo snap install qxmledit
# 路徑在 /snap/bin/qxmledit
./snap/bin/qxmledit
```


### Visual Studio 2019

聽說 2019 有圖示介面可以看
但我不想裝大怪獸...

參考來源:
- [xsd - How to visualize an XML schema? - Stack Overflow](https://stackoverflow.com/questions/2486758/how-to-visualize-an-xml-schema)
- [XML Schema editors - Wikipedia](https://en.wikipedia.org/wiki/XML_Schema_editors)

## XSD 教學

[XML：Schema、三种编写Schema的方式 - 熊诗言的博客 - CSDN博客](https://blog.csdn.net/xxssyyyyssxx/article/details/50372503)
[DTD與XSD的區別 - IT閱讀](https://www.itread01.com/content/1548934564.html)


