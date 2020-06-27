---
title: Java 因憑證 SNI 問題驗證不過
date: 2020-06-25 23:49:03
tags:
categories:
---

最近在做 line webhook
但是做測試的時候
發現沒有呼叫我的程式
但我手機、其他電腦都可以連線
這又非常詭異

<!--more-->

## 使用外部網站檢查

我在[SSL Server Test (Powered by Qualys SSL Labs)](https://www.ssllabs.com/ssltest/)做測試
是在這裡面找到的[edelahozuah/awesome-tls-security: A collection of (not-so, yet) awesome resources related to TLS, PKI and related stuff](https://github.com/edelahozuah/awesome-tls-security)

這個網站可以找到很多問題
但主要不能連我是遇到的問題是`Client aborts on SNI unrecognized_name warning`

{% asset_img error1.png "Java SNI 憑證問題" %}



## 使用 Java SSLTEST

不多說，紀錄安裝過程

```bash
# 安裝 jdk
sudo apt install default-jdk #openjdk-11-jdk

# 下載 ssltest
git clone https://github.com/ChristopherSchultz/ssltest.git

cd ssltest
./build.sh # 記得 socure 跟 target 要改11 因為我使用的是11
cd build
java -jar ssltest.jar google.com
# 我第一次的實用適用 jdk 7 跑，會有 sni Exception
# 但我現在用 jdk 11 沒有跑出 Exception
# 不過可以看到沒有 Accepted
java -jar ssltest.jar  google.com.tw | grep Accepted


```

自己來寫一隻來驗證
其實用 postman 產生範例
搭配我之前 maven 執行
```java
OkHttpClient client = new OkHttpClient();

Request request = new Request.Builder()
  .url("http://httpbin.org/get?test=123%20123")
  .get()
  .addHeader("cache-control", "no-cache")
  .addHeader("postman-token", "b0cbc43f-9a70-1056-951e-e5b0c6b2dd33")
  .build();

Response response = client.newCall(request).execute();
```

maven 不知道為什麼執行要加這段` -Dexec.cleanupDaemonThreads=false`
[运行 mvn java:exec 如何避免 `IllegalThreadStateException` - 尚码园](https://www.shangmayuan.com/a/405034f1ec9c45fe8e1aa411.html)

```
[WARNING] 
javax.net.ssl.SSLHandshakeException: received handshake warning: unrecognized_name
    at sun.security.ssl.Alert.createSSLException (Alert.java:131)
    at sun.security.ssl.Alert.createSSLException (Alert.java:117)
    at sun.security.ssl.TransportContext.fatal (TransportContext.java:313)
    at sun.security.ssl.TransportContext.fatal (TransportContext.java:269)
    at sun.security.ssl.TransportContext.fatal (TransportContext.java:260)
    at sun.security.ssl.Alert$AlertConsumer.consume (Alert.java:272)
    at sun.security.ssl.TransportContext.dispatch (TransportContext.java:186)
    at sun.security.ssl.SSLTransport.decode (SSLTransport.java:164)
    at sun.security.ssl.SSLSocketImpl.decode (SSLSocketImpl.java:1144)
    at sun.security.ssl.SSLSocketImpl.readHandshakeRecord (SSLSocketImpl.java:1055)
    at sun.security.ssl.SSLSocketImpl.startHandshake (SSLSocketImpl.java:395)
    at okhttp3.internal.connection.RealConnection.connectTls (RealConnection.kt:367)
    at okhttp3.internal.connection.RealConnection.establishProtocol (RealConnection.kt:325)
    at okhttp3.internal.connection.RealConnection.connect (RealConnection.kt:197)
    at okhttp3.internal.connection.ExchangeFinder.findConnection (ExchangeFinder.kt:249)
    at okhttp3.internal.connection.ExchangeFinder.findHealthyConnection (ExchangeFinder.kt:108)
    at okhttp3.internal.connection.ExchangeFinder.find (ExchangeFinder.kt:76)
    at okhttp3.internal.connection.RealCall.initExchange$okhttp (RealCall.kt:245)
    at okhttp3.internal.connection.ConnectInterceptor.intercept (ConnectInterceptor.kt:32)
    at okhttp3.internal.http.RealInterceptorChain.proceed (RealInterceptorChain.kt:100)
    at okhttp3.internal.cache.CacheInterceptor.intercept (CacheInterceptor.kt:96)
    at okhttp3.internal.http.RealInterceptorChain.proceed (RealInterceptorChain.kt:100)
    at okhttp3.internal.http.BridgeInterceptor.intercept (BridgeInterceptor.kt:83)
    at okhttp3.internal.http.RealInterceptorChain.proceed (RealInterceptorChain.kt:100)
    at okhttp3.internal.http.RetryAndFollowUpInterceptor.intercept (RetryAndFollowUpInterceptor.kt:76)
    at okhttp3.internal.http.RealInterceptorChain.proceed (RealInterceptorChain.kt:100)
    at okhttp3.internal.connection.RealCall.getResponseWithInterceptorChain$okhttp (RealCall.kt:197)
    at okhttp3.internal.connection.RealCall.execute (RealCall.kt:148)
    at test.App.main (App.java:23)
    at org.codehaus.mojo.exec.ExecJavaMojo$1.run (ExecJavaMojo.java:254)
    at java.lang.Thread.run (Thread.java:834)
```

正在就能證明 JAVA 程式會有問題

## Java 其他驗證

這個也可以直接看到 Exception 錯誤內容

[UniconLabs/java-keystore-ssl-test: A simple script to verify SSL/HTTPS functionality of a remote host through JDK keystore.](https://github.com/UniconLabs/java-keystore-ssl-test)

```bash
mvn clean package
cd target
java -jar java-keystore-test-0.1.0.jar https://google.com.tw
```

## 其他程式

```bash
curl https://xxx
```

golang 就用 postman 產生出來吧

## 解決方法

apache  VirtualServer 設定

ServerName xxx

```xml
<VirtualHost xxx:443>
ServerName www.example.com
ServerAlias example.com www.example.com
...
```

[Client aborts on SNI unrecognized_name warning](https://qualys-secure.force.com/discussions/s/question/0D52L00004TnvXySAJ/client-aborts-on-sni-unrecognizedname-warning)
[Qualys Labs SSL Test - Incorrect SNI alerts - JERVIS DOT WS](https://jervis.ws/qualys-labs-ssl-test-incorrect-sni-alerts/)

其他相關
[java - Handshake alert: Unrecognized_name Liberty Profile - Stack Overflow](https://stackoverflow.com/questions/37206209/handshake-alert-unrecognized-name-liberty-profile)
[10 Online Tool to Test SSL, TLS and Latest Vulnerability - Geekflare](https://geekflare.com/ssl-test-certificate/)

[drwetter/testssl.sh: Testing TLS/SSL encryption anywhere on any port](https://github.com/drwetter/testssl.sh)
git clone https://github.com/drwetter/testssl.sh
chmod a+x testssh.sh
./testssh.sh google.com

[pornin/TestSSLServer](https://github.com/pornin/TestSSLServer)
[tord and lion's lair | 各式心得 | 頁 4](https://www.bear2little.net/wordpress/?paged=4)

## 感想

之前[萬用憑證(WildCard)和中間憑證小記 | 程式狂想筆記](https://malagege.github.io/blog/2018/10/10/%E8%90%AC%E7%94%A8%E6%86%91%E8%AD%89-WildCard-%E5%92%8C%E4%B8%AD%E9%96%93%E6%86%91%E8%AD%89%E5%B0%8F%E8%A8%98/)就讓我以為就不會有其他問題
沒想到 Java 也有憑證這一關(好像是 Java 獨立的)
QQ 希望之後不要遇到類似這個問題