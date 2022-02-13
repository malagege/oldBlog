---
title: Eclipse Tomcat 錯誤問題 May be locked by another process
date: 2022-02-13 23:53:06
tags: [java,eclipse,tomcat]
categories: Java
---
遇到奇奇怪怪問題

<!--more-->

參照:
- [Tomcat报错，May be locked by another process._bingguang1993的博客-CSDN博客](https://blog.csdn.net/bingguang1993/article/details/84860635)

- [Eclipse每次修改檔案之後，tomcat要自動重新部署，就會報這個錯誤：May be locked by another process - IT閱讀](https://www.itread01.com/content/1547198852.html)

- [關於tomcat被鎖後 ,報錯could not delete的解決方法 | 程式前沿](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/554892/)


測試好像沒用，最後發現不是以上問題，所以要把 Eclipse Clean完後，處理中的Validating XML/JSP 要等他跑完，再按 Publish 程式，我的就不會有這個問題。

不過Validating XML/JSP 真的很久，最後參考[Disable XML validation in Eclipse - Stack Overflow](https://stackoverflow.com/questions/7489510/disable-xml-validation-in-eclipse)

![](https://i.imgur.com/81m2gIt.png)


先暫時把 XML 關掉，但我也不確定會不會影響到程式，所以JSP沒先關掉。