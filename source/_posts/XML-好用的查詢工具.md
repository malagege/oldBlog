---
title: XML 好用的查詢工具
date: 2019-09-05 23:05:07
tags: [xml, bash, vscode]
categories: XML
---

最近做 Digital Data Exchange 介接資料
XML 平常是會用，有知道一些 Library 可以解析裡面資料
但進階的 xpath 平常沒有什麼用到
就找找看有什麼實用工具
讓未來 debug 有更快的方法

<!--more-->

首先 Digital Data Exchange 是一個規則(XML)
因為可能會用到 xpath 在想有什麼好工具可以用?

## XSD

首先，先講解什麼是 XSD(XML Schema D)
我以前這篇文章有提過[驗證快速檢查JSON方法 | 程式狂想筆記](https://malagege.github.io/blog/2018/10/20/%E9%A9%97%E8%AD%89%E5%BF%AB%E9%80%9F%E6%AA%A2%E6%9F%A5JSON%E6%96%B9%E6%B3%95/)

[使用 XSD 為 XML 客製 Intellisense 輸入選單 - Yowko's Notes](https://blog.yowko.com/xml-intellisense-xsd/)
[XSDVi](http://xsdvi.sourceforge.net/)
[Dgis XSD Diagram](http://regis.cosnier.free.fr/?page=XSDDiagram)

但我還沒找到好用的工具

## bash 指令

### xmllint


```bash
# 安裝
sudo apt install libxml2-utils
# 使用 xpath
xmllint --xpath "//*[local-name()='product_version']/*[local-name()='name']/text()" file.xml
```

不過不知到為什麼公司有裝的工具不能用
可能版本太舊?

**2019-09-10**
有找到方法
[xmllint unknown option '--xpath' - Stack Overflow](https://stackoverflow.com/questions/11975862/xmllint-unknown-option-xpath)

結果查東西還是要用人工去掃...
```bash
xmllint --shell xxxx.xml << EOF
'//xxx'
EOF



xmllint --shell xxxx.xml <<<'xpath //xxx'


for f in `find . -type f  -name '*.xml'`
do
xmllint --shell $f <<<'xpath //oooo/' 
done


for f in `find . -type f  -name '*.xml'`
do
echo $f
xmllint --shell $f <<<'xpath //oooo[.!="aa" and .!="bbb" and .!="ccc"]/text()'
done

for f in *.xml
do
echo $f
done

for f in `find . -type f  -name '*.xml'`
do
echo $f
xmllint --shell $f <<<'xpath //xxx[.="123456"]/text()'
done

```

## xpath

```bash
# 安裝
sudo apt install libxml-xpath-perl
# xpath
xpath -e '//book/name/text()'  sample.xml
```

## xml2

```bash
# 安裝
sudo apt install xml2
cat sample.xml |xml2
```

詳細可看: [Shell解析处理XML方法汇总](https://blog.mythsman.com/post/5d2b5ebf25601931a5f8d885/) {% asset_link web1.png 備份圖 %}

其他參考
* [如何使用XMLLINT和BASH解析XML文件中的命名空间 - 智库101 - 一个基于CC版权的问答分享平台](http://www.kbase101.com/question/25515.html)
* [/bin/bash: xmllint: 未找到命令 - hailushijie的专栏 - CSDN博客](https://blog.csdn.net/droyon/article/details/8655517)
* [Shell解析处理XML方法汇总](https://blog.mythsman.com/post/5d2b5ebf25601931a5f8d885/)
* [XPath详解,总结 - 钱途无梁 - 博客园](https://www.cnblogs.com/qiantuwuliang/archive/2009/05/17/1458684.html)
* [xpath注入详解 - 渗透测试中心 - 博客园](https://www.cnblogs.com/backlion/p/8554749.html)


## 實用 vscode 套件

[XML - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml)
記得要安裝 Java
要設定 home 路徑
[XML Tools - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=DotJoshJohnson.xml)
可查 xpath ，算是滿方便可以查詢套件!!


## 相關 xpath 一些教學網站

因為還沒有很熟悉，之後開一篇做 xpath 筆記
XSD 可能也會記錄怎麼看，感覺認真看 XML 也是個大坑!!!

* [XPath详解,总结 - 钱途无梁 - 博客园](https://www.cnblogs.com/qiantuwuliang/archive/2009/05/17/1458684.html)
* [xpath注入详解 - 渗透测试中心 - 博客园](https://www.cnblogs.com/backlion/p/8554749.html)
* [Nightwatch101 #6：使用 Xpath 定位網頁元素 | Summer。桑莫。夏天](https://cythilya.github.io/2017/12/16/nightwatch-xpath/)