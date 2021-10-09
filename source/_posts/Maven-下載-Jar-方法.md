---
title: Maven 下載 Jar 方法
date: 2021-10-09 23:19:08
tags: [java,maven,jar]
categories: Java

---

之前就有簡單小記[springframework3 with struts2 使用 swagger 工具注解使用 | 程式狂想筆記](https://malagege.github.io/blog/2021/01/04/springframework3-with-struts2-%E4%BD%BF%E7%94%A8-swagger-%E5%B7%A5%E5%85%B7%E6%B3%A8%E8%A7%A3%E4%BD%BF%E7%94%A8/)

<!--more-->

## 安裝

建置資料夾，新增 pom.xml Maven設定檔。

```xml=
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.yiibai.core</groupId>
  <artifactId>ProjectName</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>ProjectName</name>
  <url>http://maven.apache.org</url>
  <dependencies>
<!-- https://mvnrepository.com/artifact/org.docx4j/docx4j -->
<dependency>
    <groupId>org.docx4j</groupId>
    <artifactId>docx4j</artifactId>
    <version>3.2.2</version>
</dependency>

  </dependencies>
</project>

```

下這個指令。
```bash=
mvn dependency:copy-dependencies
```

就可以下載。