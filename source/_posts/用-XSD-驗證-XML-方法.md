---
title: 用 XSD 驗證 XML 方法
date: 2021-02-02 21:54:26
tags:
categories:
---

維運常常遇到 XML 裡面格式
規則不是很清楚
後來

<!--more-->

## Notepad++

簡單好用!!目前最推薦這個方法

[Validate XML with XSD in Notepad++ - YouTube](https://www.youtube.com/watch?app=desktop&v=hvIKAD1vMCo)

## VScode

[XML - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml)

要特別安裝Java 才能用

## 使用 xmllint 指令確認

```bash
xmllint -schema  C0401.xsd  C0401_42526317_PX91201429_20210817180616000.xml  --noout
```

參考:
- [linux - how to Validate a XML file with XSD through xmllint - Stack Overflow](https://stackoverflow.com/questions/42809088/how-to-validate-a-xml-file-with-xsd-through-xmllint)
- [xmllint : validate an XML file against two XSD schemas (envelope / payload) - Stack Overflow](https://stackoverflow.com/questions/17002324/xmllint-validate-an-xml-file-against-two-xsd-schemas-envelope-payload/17003036)

### 找到 MIG XSD

- [瑞曲之聲: C# 簡易電子發票資料Turnkey交換範例](https://richpercussion.blogspot.com/2017/10/c.html)
- [不好意思，因為沒聯絡方式，所以只好在這邊留言詢問 · Issue #1 · RichieHsu/ENVITest](https://github.com/RichieHsu/ENVITest/issues/1)
- [ENVITest/CSharp/ENVITest/EInvoiceXSD/v31 at master · RichieHsu/ENVITest](https://github.com/RichieHsu/ENVITest/tree/master/CSharp/ENVITest/EInvoiceXSD/v31)
- [電子發票-TurnKey用XML相關參考 | 程式猿Fighting! - 點部落](https://dotblogs.com.tw/programmerfighting/2018/07/26/163310)