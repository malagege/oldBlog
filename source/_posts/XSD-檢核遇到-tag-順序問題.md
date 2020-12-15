---
title: XSD 檢核遇到 tag 順序拿掉不能放 unbounded
date: 2020-10-15 20:09:18
tags: [xsd]
categories: XML
---

如題
xs:sequence 會檢查 xml tag 順序
[xml - how can i remove the ordering from a xsd sequence - Stack Overflow](https://stackoverflow.com/questions/23103526/how-can-i-remove-the-ordering-from-a-xsd-sequence)
要排除這個方法要 xs:all
但會遇到 unbounded 不能正常運作

最後找到一篇解決方法

<!--more-->

[[XML] 讓 XML schema 允許 XML 中的元素多次出現、且順序不固定 | EPH 的程式日記](https://ephrain.net/xml-%E8%AE%93-xml-schema-%E5%85%81%E8%A8%B1-xml-%E4%B8%AD%E7%9A%84%E5%85%83%E7%B4%A0%E5%A4%9A%E6%AC%A1%E5%87%BA%E7%8F%BE%E3%80%81%E4%B8%94%E9%A0%86%E5%BA%8F%E4%B8%8D%E5%9B%BA%E5%AE%9A/)

```xsd
<xsd:element name="event" minOccurs="1" maxOccurs="unbounded">
  <xsd:complexType>
    <xsd:sequence>
      <xsd:choice minOccurs="1" maxOccurs="unbounded">
        <xsd:element type="xsd:string" name="keyword"/>
        <xsd:element type="xsd:string" name="except"/>
      </xsd:choice>
    </xsd:sequence>
  </xsd:complexType>
</xsd:element>
```

程式過了，太神啦!! 為什麼sequence 裡面可以放 choice ??
先不管啦!! 又解決這一關

奇怪的事情，開發環境竟然原本用 xsd:sequence 會過(Window)
但在正式環境就不會...

其他相關

* [XSD 复合类型指示器](https://www.w3school.com.cn/schema/schema_complex_indicators.asp)
* [XSD/XML Schema 学习笔记完整版 | C_Meng PSNA](https://imonce.github.io/2019/03/15/XSD%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%E5%AE%8C%E6%95%B4%E7%89%88/)
* [xml - how can i remove the ordering from a xsd sequence - Stack Overflow](https://stackoverflow.com/questions/23103526/how-can-i-remove-the-ordering-from-a-xsd-sequence)
* [[XML] 讓 XML schema 允許 XML 中的元素多次出現、且順序不固定 | EPH 的程式日記](https://ephrain.net/xml-%E8%AE%93-xml-schema-%E5%85%81%E8%A8%B1-xml-%E4%B8%AD%E7%9A%84%E5%85%83%E7%B4%A0%E5%A4%9A%E6%AC%A1%E5%87%BA%E7%8F%BE%E3%80%81%E4%B8%94%E9%A0%86%E5%BA%8F%E4%B8%8D%E5%9B%BA%E5%AE%9A/)