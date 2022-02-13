---
title: Excel 遇到奇怪空白字元(U+00A0)
date: 2022-02-13 23:23:50
tags: [excel,奇怪空白]
categories: 程式心法
---



最近系統上傳Excel匯入功能發生問題，查詢發現資料庫匯入時候，數值莫名其妙會帶入空白，查詢發現是`U+00A0`問題，最後紀錄一下排除` `方法。


參考:
- [Unicode 查询](https://unicode.yunser.com/unicode)
查找奇怪字元
![](https://i.imgur.com/DVPTrTx.png)
- [不換行空格 - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/%E4%B8%8D%E6%8D%A2%E8%A1%8C%E7%A9%BA%E6%A0%BC)
- [POI导入Excel，字符串格式出现空格，trim无效？_乐闻世界-CSDN博客_poi 空格](https://blog.csdn.net/m0_37890289/article/details/84256867)
- [java - Apache POI Anomalous Whitespace (Resolved: \u00A0 non-breaking space) - Stack Overflow](https://stackoverflow.com/questions/30630625/apache-poi-anomalous-whitespace-resolved-u00a0-non-breaking-space)
- [“space” Unicode Characters & Entities Search | Amp What](http://www.amp-what.com/unicode/search/space)

<!--more-->

## 程式解決方法


### 方法1

```java=
     /**
     * 去空格
     * @param str
     * @return
     */
    private  String StringTrim(String str){
        return str.replaceAll("[\\s\\u00A0]+","").trim();
    }
```

參考:[POI导入Excel，字符串格式出现空格，trim无效？_乐闻世界-CSDN博客_poi 空格](https://blog.csdn.net/m0_37890289/article/details/84256867)

### 方法2(推薦)

全形空白也能過濾。

```
string.replaceAll("(^\\h*)|(\\h*$)","")

```

[string - How to trim no-break space in Java? - Stack Overflow](https://stackoverflow.com/questions/28295504/how-to-trim-no-break-space-in-java)


## 造成原因

```
&nbsp;&nbsp;&nbsp;&nbsp;test&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;test
```

顯上網頁內文貼到Excel會有問題。

![](https://i.imgur.com/tQGW1ao.png)

### 使用者排除方法

1. 複製到txt再複製到Excel。
2. 貼到Excel 記得使用存文字貼上。

以上方法可排除

## 快速查詢方法


[regex101: build, test, and debug regex](https://regex101.com/r/RZatWj/1)

```
[^\d\.\s]
```

![](https://i.imgur.com/tiYzSSn.png)


何時會再踩到這個雷，不知道XD。