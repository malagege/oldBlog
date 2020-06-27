---
title: Spring 自動注入小記
date: 2020-05-11 23:41:17
tags: [spring, junit]
categories: Java
---

自動注入筆記

<!--more-->

## autowired 加載方式


### 建構子

### 成員變數

### setter method

### 任意 method

## XML 自動掛載

```xml
<content:component-scan base-package="com.xxxx.oooo">
```

## 自動載入相關 annotation

### 自動載入

組間掃描
@Component
@ComponentScan

自動注入
@Autowired

### 定義配置類

@Configuration(classes=AppConfig.class)

### JUnit

#### 安裝

1. 設定 JUnit Library 加載
pom.xml 設定 spring-test,junit 如下設定參考
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.qfedu</groupId>
  <artifactId>spring01demo</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <dependencies>
	<!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-context</artifactId>
	    <version>4.3.13.RELEASE</version>
	</dependency>
	<dependency>
		<groupId>log4j</groupId>
		<artifactId>log4j</artifactId>
		<version>1.2.17</version>
	</dependency>
	<dependency>
		<groupId>junit</groupId>
		<artifactId>junit</artifactId>
		<version>4.12</version>
	</dependency>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-test</artifactId>
		<version>4.3.13.RELEASE</version>
	</dependency>
  </dependencies>
</project>
```


2. 設定 test 程式加入 設定
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = AppConfig.class)

**這邊注意

```java
package hello;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = AppConfig.class)
public class testHello {

	@Autowired
	private MessagePrinter messagePrinter;
	
	@Test
	public void testPrint() {
		messagePrinter.printMessage();
	}
}

```

3. 自動載入
@Autowired

下面程式參考:[JUnit高级用法之@RunWith - 摆渡者 - OSCHINA](https://my.oschina.net/itblog/blog/1550753)
```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = { "classpath:applicationContext.xml" })
public class UserManagerTest {
　　@Autowired
　　ApplicationContext ctx;

　　@Test
　　public void testAddUser() {
　　　　try {
　　　　　　UserManager userManager = ctx.getBean(UserManager.class); 
　　　　　　userManager.addUser();
　　　　} catch (Exception e) {
　　　　　　e.printStackTrace();
　　　　}
　　}
}
```

## 自動配裝衝突

1. @Primary
在 Component 定義 @primary

2. @Qualifier
默認 `宣告` 和 `配裝` 使用 @Qualifier

3. @Qualifier
自動配裝可使用 bean id(第一個小寫)
@Qualifier("helloWorld")

## 相關載入事情

1. @Resource( name = "helloWorld" )

`@Resource( name = "helloWorld" )` 等於
```
@Autowired
@Qualifier( name = "helloWorld" )
```

2. @Controller , @Service, @Repository
`@Controller , @Service, @Repository` 等於 @Component 相同功能
比較有語意，跟 @Component 沒什麼差別。

## Autowired (require = false )

if(xxx == null)

## 指定掃描

1. @ComponentScan( "com.xxx.demo" )
掃描底下 "com.xxx.demo"的所有 Package

2. @ComponentScan( basePackages = {"com.xxx.demo.web", "com.xxx.demo.service", "com.xxx.demo.dao"})
解決上面不能放多個

2. @ComponentScan( basePackageClasses = {MessagePrinter.class, MessageService.class})
上面嚴重缺點，不能自動用程式"重構"功能，連帶修改




## 小記

這邊的 @Autowired 預設都是**單例模式**
額且啟動服務(這邊講的是 Web )的時候，這些套件就會自動載入

這邊也順便紀錄一下，Servlet 生命週期，init() 是 request 觸發的時候會觸發
Servlet 其實也算是只會實例一個，不是多個 Servlet 程式
所以在多執行緒需要注意一下問題...