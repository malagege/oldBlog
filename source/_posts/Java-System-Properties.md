---
title: Java System Properties
date: 2022-02-13 23:28:23
tags: [java,properties]
categories: Java
---

常常看到`java -Dxxx="test" com.xxx.Class` 程式，不太了解`-D`帶入程式運作，今天特別整理一下，順便筆記。

<!--more-->


## 抓取 Java 系統參數(非環境變數)

```
java.version 	Java 執行期環境版本
java.vendor 	Java 執行期環境供應商
java.vendor.url 	Java 供應商的 URL
java.home 	Java 安裝目錄
java.vm.specification.version 	Java 虛擬機規範版本
java.vm.specification.vendor 	Java 虛擬機規範供應商
java.vm.specification.name 	Java 虛擬機規範名稱
java.vm.version 	Java 虛擬機實現版本
java.vm.vendor 	Java 虛擬機實現供應商
java.vm.name 	Java 虛擬機實現名稱
java.specification.version 	Java 執行期環境規範版本
java.specification.vendor 	Java 執行期環境規範供應商
java.specification.name 	Java 執行期環境規範名稱
java.class.version 	Java 類別格式版本號
java.class.path 	Java 類別路徑
java.library.path 	加載庫時搜索的路徑串列（linked-list）
java.io.tmpdir 	預設的暫時檔路徑
java.compiler 	要使用的 JIT 編譯器的名稱
java.ext.dirs 	一個或多個擴展目錄的路徑
os.name 	作業系統 的名稱
os.arch 	作業系統 的架構
os.version 	作業系統 的版本
file.separator 	檔分隔設定（在 UNIX 系統中是“/”）
path.separator 	路徑分隔設定（在 UNIX 系統中是“:”）
line.separator 	行分隔設定（在 UNIX 系統中是“/n”）
user.name 	用戶的賬戶名稱
user.home 	用戶的主目錄
user.dir 	用戶的當前工作目錄

```

但還是可以自訂。


要怎麼看程式有哪些 System Properties，可以參考[Java System Properties - HowToDoInJava](https://howtodoinjava.com/java/basics/java-system-properties/#get-property)。

```java=
import java.util.Properties;
 
public class PrintSystemProperties 
{
   public static void main(String[] a) 
   {
      // List all System properties
      Properties pros = System.getProperties();
      pros.list(System.out);
   }
}
```

```bash=
java -DPATH PrintSystemProperties
```

![](https://i.imgur.com/2QAbEBt.png)


```bash=
java -DPATH="123Test" PrintSystemProperties
```
![](https://i.imgur.com/THTX5R1.png)


## 抓取環境變數

```java=
String sysEnvStr = System.getenv("JAVA_HOME");
```

參考: [java - How to I obtain the value of the environment variables? - Stack Overflow](https://stackoverflow.com/questions/4906292/how-to-i-obtain-the-value-of-the-environment-variables/4906550)

## 其他相關:


[System (Java Platform SE 7 )](https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getProperty(java.lang.String))

[System.getProperty的參數用法 @ 符碼記憶](https://www.ewdna.com/2009/06/systemgetproperty.html)


## Java 使用 Docker run 選擇方法

1. 一般方式
參考:
[java - How to pass System property to docker containers? - Stack Overflow](https://stackoverflow.com/questions/33408626/how-to-pass-system-property-to-docker-containers)

2. JAVA_OPTIONS 環境變數設定(推薦)

```
JAVA_OPTIONS: "-Xmx2g -Dcom.realobjects.pdfreactor.webservice.threadPoolSize=4"
```
參考:
[Docker conatiner: Get passed environment varaible in tomcat spring mvc application - Server Fault](https://serverfault.com/questions/880019/docker-conatiner-get-passed-environment-varaible-in-tomcat-spring-mvc-applicati)

3. 一般run 指定程式執行路徑，順便放system properties