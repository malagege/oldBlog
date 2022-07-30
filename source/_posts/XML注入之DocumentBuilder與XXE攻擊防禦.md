---
title: XML注入之DocumentBuilder與XXE攻擊防禦
date: 2022-07-30 22:45:26
tags: [java,xml,inject]
categories: 程式風險
---

成事源碼掃描掃到這個問題，平常很少使用 XML ，沒有深入這個東西。遇到這個錯誤不知道是什麼?
但查看原理感覺還滿嚴重，不過我照網路上解決方法再送出源掃，竟然可以通過，順便筆記紀錄一下。

<!--more-->

參考文章:

[XML注入之DocumentBuilder與XXE攻擊防禦 [ Mi1k7ea ]](https://www.mi1k7ea.com/2019/02/13/XML%E6%B3%A8%E5%85%A5%E4%B9%8BDocumentBuilder/)


[Improper Restriction of XXE Ref | 亂馬客 - Re:從零開始的軟體開發生活](https://rainmakerho.github.io/2020/11/10/Improper-Restriction-of-XXE-Ref/)


[XXE防禦筆記_Kingkk's Blog - MdEditor](https://www.gushiciku.cn/pl/2SvN/zh-tw)

[XML External Entity Prevention - OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html)




```java
DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
String FEATURE = null;
try {
    // This is the PRIMARY defense. If DTDs (doctypes) are disallowed, almost all 
    // XML entity attacks are prevented
    FEATURE = "http://apache.org/xml/features/disallow-doctype-decl";
    dbf.setFeature(FEATURE, true);

    // If you can't completely disable DTDs, then at least do the following
    FEATURE = "http://xml.org/sax/features/external-general-entities";
    dbf.setFeature(FEATURE, false);

    FEATURE = "http://xml.org/sax/features/external-parameter-entities";
    dbf.setFeature(FEATURE, false);

    // Disable external DTDs as well
    FEATURE = "http://apache.org/xml/features/nonvalidating/load-external-dtd";
    dbf.setFeature(FEATURE, false);

    // and these as well, per Timothy Morgan's 2014 paper: "XML Schema, DTD, and Entity Attacks"
    dbf.setXIncludeAware(false);
    dbf.setExpandEntityReferences(false);
} catch (Exception e) {
	...
}
DocumentBuilder safebuilder = dbf.newDocumentBuilder();
```