---
title: 'JSP 一些常看標籤小記(JSTL, EL, ONGL)'
date: 2020-05-10 15:16:14
tags: [jstl, ongl, el]
categories: Java
---

最近使用 struts2 `<s:property value="text">` 
但在 JSP 頁面要做 if 判斷
我今天抓不到 struts2 test值
剛好網路爬文就找到這篇[EL、OGNL、JSTL和struts标签的区别_数据库_qq30211478的博客-CSDN博客](https://blog.csdn.net/qq30211478/article/details/77188991)
順便整理

<!--more-->

## 先解 struts2 我遇到的問題

`<s:if test="">` 要怎麼判斷 `<s:property value="msg">` 呢?

```
    <s:if test='messageStore == "Hello Struts User (from toString)"'>
    123456789
    </s:if>
```



[阿信 (Silver8250) 的冷泡咖啡館~: [Struts2] Struts2 Data Tags](http://silver8250.blogspot.com/2009/05/struts2-struts2-data-tags.html)


[EL、OGNL、JSTL和struts标签的区别_数据库_qq30211478的博客-CSDN博客](https://blog.csdn.net/qq30211478/article/details/77188991)


[C标签和s标签的对比，特别是里面的if判断条件的写法_java_寂寞是一个人的狂欢，狂欢是一群人的寂寞...-CSDN博客](https://blog.csdn.net/u010735684/article/details/51374484) 
[jsp中c:if与s:if的区别 - struggle_beiJing - 博客园](https://www.cnblogs.com/beijingstruggle/p/5855043.html)
