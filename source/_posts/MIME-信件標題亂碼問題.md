---
title: MIME 信件標題亂碼問題
date: 2022-07-31 02:13:58
tags: [java,mail,mime]
categories: 程式心法
---

某一天專案升級 Java 8 時候，我專案遇到Email發送標題亂碼，最後找到解決方案。
不過我還深入了解一下，預防下次遇到快速找到真正原因。

<!--more-->

```
$subject = "=?UTF-8?B?".base64_encode($subject)."?=";

將上面一句添加到代碼之中，這樣，發送的中文郵件標題就不是亂碼了。

原文網址：https://kknews.cc/code/nkobrl3.html
```

[PHP發送UTF-8編碼中文郵件標題亂碼的解決 - 每日頭條](https://kknews.cc/zh-tw/code/nkobrl3.html)

[MimeUtility (JavaMail API documentation)](https://javaee.github.io/javamail/docs/api/javax/mail/internet/MimeUtility.html)




[Java使用javax.mail傳送郵件 解決收件人、發件人名字亂碼問題 - IT閱讀](https://www.itread01.com/content/1549568006.html)

```java=
return String.format("%1$s <%2$s>", MimeUtility.encodeText(name, "UTF-8", "B"), email);
```


[MIME协议详细介绍. 邮件由header+body组成，头部格式: Name: Value。 | by Gabi Sual | Medium](https://medium.com/@liuqiang9596/mime%E5%8D%8F%E8%AE%AE%E8%AF%A6%E7%BB%86%E4%BB%8B%E7%BB%8D-cea8c03acef2)



這裡面[邮件MIME格式分析 - crystalray - 博客园](https://www.cnblogs.com/crystalray/articles/3302427.html)介紹非常詳細。