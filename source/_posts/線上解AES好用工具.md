---
title: 線上解AES好用工具
date: 2021-06-02 21:15:48
tags: [aes]
categories: 實用工具
---

平常使用API都需要複雜AES加解密轉Base64
導致測試API都很麻煩
今天找到幾個好用的網站
整理一下

<!--more-->


1. [AES decryption AES encryption AES Hex Key Supported - The X Online Tools](https://the-x.cn/en-us/cryptography/Aes.aspx)


2. [CyberChef](https://gchq.github.io/CyberChef/)

這個可以客製化很多東西

AES加解密中文Base64就發現不能顯示出來
後來研究出方法

~~加密中文出來~~中文沒法加密無法解出中文，但英文不會有問題
暫時先用上面的網站來解決
[Encode text, AES Encrypt, To Base64 - CyberChef](https://pse.is/3heuwe)
```
https://gchq.github.io/CyberChef/#recipe=Encode_text('UTF-8%20(65001)')AES_Encrypt(%7B'option':'UTF8','string':'20F1aR41efffffff'%7D,%7B'option':'UTF8','string':'20F1aR41efffffff'%7D,'CBC','Raw','Raw',%7B'option':'Hex','string':''%7D)To_Base64('A-Za-z0-9%2B/%3D')&input=YWJj5Lit5paHYWJj
```

解中文出來，後面加 decode text UTF-8 就正常了...


[From Base64, AES Decrypt, Decode text - CyberChef](https://pse.is/3h5dpr)

```
https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true)AES_Decrypt(%7B'option':'UTF8','string':'20F1aR41efffffff'%7D,%7B'option':'UTF8','string':'20F1aR41efffffff'%7D,'CBC','Raw','Raw',%7B'option':'Hex','string':''%7D,%7B'option':'Hex','string':''%7D)Decode_text('UTF-8%20(65001)')&input=QnEvc2xmcFAxSWR1YTFwUXNOV0hHUT09
```
## 翻外篇   CyberChef  強大好用的工具

### 抓取Email，去除重覆

[Extract email addresses, Unique - CyberChef](https://pse.is/3hn2a2)

```
https://gchq.github.io/CyberChef/#recipe=Extract_email_addresses(false)Unique('Line%20feed')&input=ZmZmZkBmZmYuY29tCmRkZGRAZmZmLmNvbSwgeHh4eEBmZmZmLmNvbQpmZmZmQGZmZi5jb20KZmZmZkBmZmYuY29t
```

維運很常需要做這些東西，我就不需要在 Sublime 抓出 Email 到 Excel 去除重覆

### 字串包 ",' 符號


<a href="https://gchq.github.io/CyberChef/#recipe=Extract_IP_addresses(true,false,false,false/disabled)Unique('Line%20feed')Find_/_Replace(%7B'option':'Regex','string':'(.%2B)'%7D,'%5C'$1%5C',',true,false,true,false)Sort('Line%20feed',false,'Alphabetical%20(case%20sensitive)')">Extract IP addresses, 3 more - CyberChef</a>

```
https://gchq.github.io/CyberChef/#recipe=Extract_IP_addresses(true,false,false,false/disabled)Unique('Line%20feed')Find_/_Replace(%7B'option':'Regex','string':'(.%2B)'%7D,'%5C'$1%5C',',true,false,true,false)Sort('Line%20feed',false,'Alphabetical%20(case%20sensitive)')
```

常常有大量資料查詢，都靠Sublime Text去做，有時候大量資料會當機，但用這個也能做到一樣的效果，還能做去除、排序

