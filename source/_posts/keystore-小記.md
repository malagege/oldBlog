---
title: keystore 小記
date: 2020-07-28 21:31:36
tags: [java]
categories: Linux
---

最近研究憑證，知道 Java 管理憑證機制是用自己的
keystore 這個是 Java 用的憑證
本篇不實作，簡單整理網路上的資訊

<!--more-->

## 什麼是 keystore

> Keytool是Java證書的管理工具，Keytool將金鑰（key）、證書（certificates）存在一個keystore的檔案裡

[利用keystore 指令來確認憑證密碼 - 可丁丹尼 @ 一路往前走2.0](https://cms.35g.tw/coding/keystore-check-password/)

## jssecacerts

>JSSE簡介
>
>Java安全套接擴展 (Java Secure Socket Extension, JSSE)是實現Internet安全通信的一系列包的集合。它是一個>SSL和TLS的純Java實現，可以透明地提供數據加密、服務器認證、信息完整性等功能，可以使我們像使用普通的套接字一樣使>用JSSE建立的安全套接字。JSSE是一個開放的標準，不只是Sun公司才能實現一個JSSE，事實上其他公司有自己實現的JSSE。
>
>在深入瞭解JSSE之前，需要瞭解一個有關Java安全的概念：客戶端TrustStore文件。客戶端的TrustStore文件中保存著被客戶端所信任的服務器證書信息，客戶端在嘗試進行SSL連接時，JSSE將根據這個文件中的證書是否決定信任服務端的證書。
>
>JSSE中有一個信任管理器負責決定是否信任遠程服務器的證書，該類有如下處理原則：
>
>    如果該系統屬性javax.net.sll.trustStore指定了TrustStore文件，那麼信任管理器就去jre安裝路徑下的lib/security/目錄中尋找並使用這個文件來檢查證書。如果該系統沒有指定TrustStore文件，它就會去jre安裝路徑下尋找默認的TrustStore文件，這個文件的相對路徑為：lib/security/jssecacerts。如果jssecacerts不存在，但是cacerts存在(它隨J2SDK一起發行，含有數量有限的可信任的基本證書)，那麼這個默認的TrustStore文件就是cacerts。Java提供了一種非常簡潔的方法來訪問HTTPS網頁，即使用類HttpsURLConnection、URL等。這些類為支持HTTPS對JSSE相關類做了一些封裝。 

[用Java實現HTTPS工作原理（二） - 知乎](https://zhuanlan.zhihu.com/p/75456673)

最近專案看到 jssecacerts
從這個上面我猜測是管理根憑證東西
Java 預設是沒有加入根憑證的，所以沒辦法使用。

要怎麼測試憑證 Java 吃的到?
[Java 因憑證 SNI 問題驗證不過 | 程式狂想筆記](https://malagege.github.io/blog/2020/06/25/Java-%E5%9B%A0%E6%86%91%E8%AD%89-SNI-%E5%95%8F%E9%A1%8C%E9%A9%97%E8%AD%89%E4%B8%8D%E9%81%8E/)
這篇有講到檢查憑證方法
可以指定根憑證檢查檔案
```bash
java -Djavax.net.ssl.trustStore=/path.../jssecacerts  -jar java-keystore-test-0.1.0.jar  https://xxx.twca.domain
```

檢查也有找到這個方法可用[[JAVA] SSL handshake連線測試工具 SSLPoke | 阿輝的零碎筆記 - 點部落](https://dotblogs.com.tw/grayyin/2018/07/12/145718)

SSLPoke.java
```java
import java.io.PrintStream;
import javax.net.ssl.SSLHandshakeException;
import javax.net.ssl.SSLSocket;
import javax.net.ssl.SSLSocketFactory;

public class SSLPoke
{
  public SSLPoke() {}
  
  public static void main(String[] paramArrayOfString)
  {
    if (paramArrayOfString.length != 2) {
      System.err.println("Utility to debug Java connections to SSL servers");
      System.err.println("Usage: ");
      System.err.println("  java " + SSLPoke.class.getName() + " <host> <port>");
      System.err.println("or for more debugging:");
      System.err.println("  java -Djavax.net.debug=ssl " + SSLPoke.class.getName() + " <host> <port>");
      System.err.println();
      System.err.println("Eg. to test the SSL certificate at https://localhost, use");
      System.err.println("  java " + SSLPoke.class.getName() + " localhost 443");
      System.exit(1);
    }
    try {
      SSLSocketFactory localSSLSocketFactory = (SSLSocketFactory)SSLSocketFactory.getDefault();
      SSLSocket localSSLSocket = (SSLSocket)localSSLSocketFactory.createSocket(paramArrayOfString[0], Integer.parseInt(paramArrayOfString[1]));
      
      java.io.InputStream localInputStream = localSSLSocket.getInputStream();
      java.io.OutputStream localOutputStream = localSSLSocket.getOutputStream();
      

      localOutputStream.write(1);
      
      while (localInputStream.available() > 0) {
        System.out.print(localInputStream.read());
      }
      System.out.println("Successfully connected");
      System.exit(0);
    }
    catch (SSLHandshakeException localSSLHandshakeException) {
      if (localSSLHandshakeException.getCause() != null) {
        localSSLHandshakeException.getCause().printStackTrace();
      } else {
        localSSLHandshakeException.printStackTrace();
      }
    } catch (Exception localException) {
      localException.printStackTrace();
    }
    System.exit(1);
  }
}
```


```
## 先打包成class
$ javac SSLPoke.java
$ java SSLPoke www.google.com 443
```
連線成功會顯示
```
$ java SSLPoke www.google.com 443
Successfully connected
```
失敗就會出現一大堆錯

看根憑證

```
# 查詢keystore的內容
      keytool –list –v –keystore .keystore
```
[Java keytool 基本指令 | 阿輝的零碎筆記 - 點部落](https://dotblogs.com.tw/grayyin/2019/05/04/143637)

加入 TWCA 根憑證

```bash
keytool -importcert -file "twca root ca.cer"  -keystore jssecacerts
```
jssecacerts 是放憑證
Java 存放憑證路徑

C:\Program Files\Java\jdk-13.0.2\lib\security

Linux 待補(有想到的話)，其實也是放在相似地方


本篇到這邊

查詢整理
* [Java keytool 基本指令 | 阿輝的零碎筆記 - 點部落](https://dotblogs.com.tw/grayyin/2019/05/04/143637)
* [我的工作日記: Java Keytool的使用及申請憑證(以Microsoft Active Directory Certificate Services為例)](http://polinwei.blogspot.com/2013/02/java-keytoolmicrosoft-active-directory.html)
* [Java Keytool使用方式 - SSL憑證推薦網 | SSL憑證 | code Sign](https://sslbuyer.com/index.php?option=com_content&view=article&id=134:keytool-usage&catid=25&Itemid=4031)
* [使用自签名证书，简单步骤 - 凝雨 - Yun](https://ningyu1.github.io/site/post/52-ssl-cert-2/)
* [利用keytool 匯入CA Root 憑證方式 - 寰宇數位認證中心 - 技術支援 Support](https://www.ssl.com.tw/Support/title_show.asp?id1=7&id2=1,0&titleid=249)
* [ssl - java - path to trustStore - set property doesn't work? - Stack Overflow](https://stackoverflow.com/questions/2138574/java-path-to-truststore-set-property-doesnt-work)
* [[JAVA] SSL handshake連線測試工具 SSLPoke | 阿輝的零碎筆記 - 點部落](https://dotblogs.com.tw/grayyin/2018/07/12/145718)
* [Use SSL Poke to test Java SSL connection - matthewdavis111](https://matthewdavis111.com/java/poke-ssl-test-java-certs/)
* [Connecting to SSL services - Atlassian Documentation](https://confluence.atlassian.com/jira/connecting-to-ssl-services-117455.html)



* [Android Studio：Keystore 建立與管理. 撰文時使用 MAC + Android Studio 2.2RC | by Kent Chen | Medium](https://medium.com/@kentchen_tw/android-studio-keystore-%E5%BB%BA%E7%AB%8B%E8%88%87%E7%AE%A1%E7%90%86-47d4afcc6e61)
* [关于keystore的简单介绍_人在旅途-CSDN博客_keystore是什么](https://blog.csdn.net/dotuian/article/details/51722300)
* [Android Keystore漫谈 - 简书](https://www.jianshu.com/p/644ddb6e3d9c)
* [crt to keystore - Google 搜尋](https://www.google.com/search?q=crt+to+keystore&client=firefox-b-d&sxsrf=ALeKk015rMyv_7BJR7XF7bp-HrBvx85Ybg:1595943967876&ei=HywgX8uLNfnUmAXO4yA&start=20&sa=N&ved=2ahUKEwiLl6XKivDqAhV5KqYKHc4xCAA4ChDw0wN6BAgMEEM&biw=1280&bih=644)
* [CRT证书转JKS证书 - 簡書](https://www.jianshu.com/p/59e2bb2befa9)
* [Keystore更新重置. 相信可能有人有遺失Keystroe的經驗，因這次筆者拿金鑰去註冊測試Fireba… | by 陳建維 Ben | 工程師求生指南(Sofware Engineer Survival Guide) | Medium](https://medium.com/%E5%B7%A5%E7%A8%8B%E5%B8%AB%E6%B1%82%E7%94%9F%E6%8C%87%E5%8D%97-sofware-engineer-survival-guide/%E5%9C%96%E8%A7%A3keystore%E6%9B%B4%E6%96%B0%E6%95%91%E6%8F%B4-55f230db4914)
* [DH技術筆記: SSL憑證建立](http://dh66.blogspot.com/2013/07/ssl.html)