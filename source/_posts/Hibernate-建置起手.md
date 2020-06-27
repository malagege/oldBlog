---
title: Hibernate 建置起手式
date: 2020-06-02 21:37:23
tags: [java,hibernate,dao]
categories: Java
---

TODO: hibernate 尚未整理完成

<!--more-->


## 介紹 Hibernate 


## 傳統 JDBC 查詢 SQL

```java
Class.forName("com.mysql.jdbc.Driver");
Connection conn = DriverManage.getConnection(url,username,password);
String sql = "SELECT * FROM user";
PreparedStatment psmt = conn.prepareStatment(sql);

ResultSet rs = pstm.executeQuery();

// while rs ....
conn.close();
```

一開始學 Java 我們都是這樣寫的
沒有仔細探討寫法

首先是 Class.forName
因為 Class.forName 有 static 執行 `DriverManager.registerDriver(new Driver)`

[JWorld@TW Java論壇 - Re:請問Class.forName()的用途](https://www.javaworld.com.tw/jute/post/view?bid=29&id=52014)

```java

public class JdbcDemo {
    public static void main(String[] args) throws SQLException, ClassNotFoundException {
        String url = "jdbc:mysql://127.0.0.1:3306/mydb";
        String username = "root";
        String password = "redhat";
        //Class.forName("com.mysql.jdbc.Driver");
        DriverManager.registerDriver(new Driver());
        Connection connection = DriverManager.getConnection(url, username, password);
        String sql = "SELECT * FROM msg";
        PreparedStatement prepareStatement = connection.prepareStatement(sql);
        ResultSet resultSet = prepareStatement.executeQuery();
        resultSet.next();
        String address = resultSet.getString("address");
        System.out.println(address);
    }
}
```
> 發現代碼，還是正常的執行了。
> 
> 總結一下: Class.forName 方法的作用，就是初始化給定的類。而我們給定的 MySQL 的 Driver 類中，它在靜態代碼塊中通過 JDBC 的 DriverManager 註冊了一下驅動。我們也可以直接使用 JDBC 的驅動管理器註冊 mysql 驅動，從而代替使用 Class.forName。

參考來源:[Java class.forname 詳解 | 菜鳥教程](https://www.runoob.com/w3cnote/java-class-forname.html)


## Hibernate


## 前置設定

### jar 加載

1. Hibernate jar 包
下載連結[5.4 series - Hibernate ORM](https://hibernate.org/orm/releases/5.4/)
lib/required,lib/jpa
底下需要載入jar


2. Hibernate 日誌 jar 包

3. DB 驅動 Jar 包
MySQL JDBC驅動程式(mysql-connector-java-*.jar)

### Maven 設定

設定檔原則上要放在 src 跟目錄，但

[Java程式教學甘仔店: Hibernate Hello World Example 專案 之 XML Mapping](http://pclevin.blogspot.com/2015/01/hibernate-hello-world-example-xml.html)


#### 探討 Eclipse 加 resource 資料夾

其中 resources 是會抓到 target 
```bash
# copy resources to target
cp -rf $PROJECT_DIR/resources/* $PROJECT_DIR/target
```
詳細可以看 javac 編譯:[第1期：抛开IDE，了解一下javac如何编译 | 毛帅的博客](https://imshuai.com/using-javac)
這篇可以有時間再做整理

## 設定 Hibernate

### 加入 dtd 用意



### 設定實體類 hbm.xml

```xml

```

### 設定 Hibernate 核心檔

需要放在 src 底下，除非有做 resource (還需要設定 Eclipse)

```xml

```

## 一般實作

1. 加載 Hibernate 核心文件

2. New sessionfactory

3. 使用SessionFactory 創建 Session 物件

4. 開起交易

5. CRUD

6. 交易提交

7. 關閉資源(Session,SessionFactory)


## hibernate utils

main 方法建置 table

## session 

save

update


delete


get

## hibernate 3 種狀態

1. 瞬時態 物件匋有id，session 沒有關聯
2. 持久態 物件有id、session 有關聯
這邊沒有做save，當正常commit的話
持久態會做資料寫入到資料庫
**一級緩存還會提到**
3. 托管態 物件有id，session 沒有關聯

## 緩存

### 一級緩存

通常使用

持久態 沒做 save/update，交易 commit 時候會寫入到資料庫

### 二級緩存

默認不使用

通常現在使用 redis 代替

生命週期是 SessionFactory

## getCurrentSession

threadlocal



### 不用 close

### 不用在大量資料 test

## 三種 API

### Query

使用 HQL

介於 實體類 和 SQL 之間

### Cir

使用實體類

### SQLQuery

原生 SQL 

## 實體類關聯

### 一對多

通常

#### 1. 設定

#### 2. 聯級

### 多對多

### 一對一


[Hibernate —— HQL与QBC的区别_tuxq5721-CSDN博客_hql和qbc](https://blog.csdn.net/u012045597/article/details/15701235)

## 問題

### org.hibernate.ObjectNotFoundException
[org.hibernate.ObjectNotFoundException: No row with the given identifier exists - keepup~ - 博客园](https://www.cnblogs.com/cn-chy-com/p/8810374.html)
org.hibernate.ObjectNotFoundException: No row with the given identifier exists

### hibernate -> Spring Data JPA

[Java Artisan / Neil Chan: 從 Spring + Hibernate 到 Spring Data JPA](http://cw1057.blogspot.com/2013/11/spring-hibernate-spring-data-jpa.html?m=1)

[SpringBoot系列 - 集成Hibernate | 飞污熊博客](https://www.xncoding.com/2017/07/03/spring/sb-hibernate.html)

[第一個hibernate程式 - 國立中山大學程式諮詢網](https://sites.google.com/a/mis.nsysu.edu.tw/cheng-shi-zi-xun-wang/java-ee-jin-jie-pian/hibernate-ji-shu/ji-chu-ru-men/jian-dan-fan-li/di-yi-gehibernate-cheng-shi)


## junit 使用 spring 做 DAO 查詢，最外層沒辦法做 dao 查詢

- [「快學springboot」在springboot中寫單元測試 - Toments 找話題](https://toments.com/2125588/)

- [Spring / Hibernate / JUnit - No Hibernate Session bound to Thread - Stack Overflow](https://stackoverflow.com/questions/734614/spring-hibernate-junit-no-hibernate-session-bound-to-thread)


## Hibernate Set / List

[【学习笔记】Hibernate中多对多不用List而用Set的原因分析_机智猫-CSDN博客_多对多关系用set还是list](https://blog.csdn.net/nthack5730/article/details/45201665)


[Hibernate的抓取策略及优化 - 簡書](https://www.jianshu.com/p/23af676b3977)
[java - What are the differences between the different saving methods in Hibernate? - Stack Overflow](https://stackoverflow.com/questions/161224/what-are-the-differences-between-the-different-saving-methods-in-hibernate)

