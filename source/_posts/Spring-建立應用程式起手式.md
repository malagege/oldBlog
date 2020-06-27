---
title: Spring 建立應用程式起手式
date: 2020-05-02 14:01:16
tags: [spring,java]
categories: Java
---

最近看了一些 Spring 教學
這篇整理一下建制 Spring 需要步驟

<!--more-->

## Eclipse 使用 Maven 建置 Spring

{% asset_img eclipse01.png title %}
{% asset_img eclipse02.png title %}
{% asset_img eclipse03.png title %}
{% asset_img eclipse04.png title %}

### 建置 Maven 預到 Eclipse 警告

參考:[Maven錯誤 – #001 – JDK預設版本1.5 – Syuan的筆記](http://twsyuan.com/2018/06/maven%E9%8C%AF%E8%AA%A4-001/)

不知道是我的 Eclipse 自帶 Maven 
我測試把 setting.xml 複製到 `C:\Users\Administrator\.m2`
我是用 Chocolatey 安裝 Maven
可以到`C:\ProgramData\Chocolatey\lib\maven` 找到 setting.xml 
因為我安裝是 1.13 JDK 版本，所以這邊調整 1.13
```xml
  <profiles>
  	<profile>   
	
    <id>jdk1.13</id>    
    <activation>   
        <activeByDefault>true</activeByDefault>    
        <jdk>1.13</jdk>   
    </activation>   
	
    <properties>   
        <maven.compiler.source>1.13</maven.compiler.source>    
        <maven.compiler.target>1.13</maven.compiler.target>    
        <maven.compiler.compilerVersion>1.13</maven.compiler.compilerVersion>   
    </properties>   
	
	</profile>
    <!-- profile
```

這樣建置新專案就不會有問題了


## 一般呼叫 Object

```java Application.java
package hello;

public class Application {

	public static void main(String[] args) {
		
		System.out.println("application");
		
		MessagePrinter printer = new MessagePrinter();
		
		MessageService service = new MessageService();
		
		printer.setService(service);
		
		printer.printMessage();
	}
}

```

```java MessagePrinter.java
package hello;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

public class MessagePrinter {
	
	/**
	 * 設定 MessageService 關係
	 */
	private MessageService service;
	
	public MessagePrinter() {
		super();
		System.out.println("MessagePrinter...");
	}

	/**
	 * 設定 service 值
	 * @param service
	 */
	@Autowired
	public void setService(MessageService service) {
		this.service = service;
	}
	
	public void printMessage() {
		System.out.print(this.service.getMessage());
	}
}

```

```java MessageService.java
package hello;

import org.springframework.stereotype.Component;

public class MessageService {

	public MessageService() {
		super();
		System.out.println("MessageService...");
	}

	/**
	 * print Hello World
	 * @return print Hello World
	 */
	public String getMessage() {
		return "Hello World";
	}
}

```

## Spring Autowired 方式(annotation)

這邊簡單說幾個 annotation 重點
@Component
@ComponentScan 標籤需要放在主程式，會掃有關 @Component
@Autowired 放在 setXXXX(MessageService service)
這邊跟我公司實戰上不時一樣
但這應該是最原始寫法

```java ApplicationSpring.java
package hello;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;

/**
 * 可以掃描 @Component
 *
 */
@ComponentScan
public class ApplicationSpring {

	private static ApplicationContext context;

	public static void main(String[] args) {
		
		System.out.println("applicationSpring");
		
		context = new AnnotationConfigApplicationContext(ApplicationSpring.class);
		
		// 從容器中獲取 MessagePrinter  物件
		MessagePrinter printer = context.getBean(MessagePrinter.class);
		
//		MessageService service = context.getBean(MessageService.class);
		
		System.out.println(printer);
//		System.out.println(service);
		
//		printer.setService(service);
		printer.printMessage();
	}
}

```

```java MessagePrinter.java
package hello;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class MessagePrinter {
	
	/**
	 * 設定 MessageService 關係
	 */
	private MessageService service;
	
	public MessagePrinter() {
		super();
		System.out.println("MessagePrinter...");
	}

	/**
	 * 設定 service 值
	 * @param service
	 */
	@Autowired
	public void setService(MessageService service) {
		this.service = service;
	}
	
	public void printMessage() {
		System.out.print(this.service.getMessage());
	}
}

```

```java MessageService.java
package hello;

import org.springframework.stereotype.Component;

@Component
public class MessageService {

	public MessageService() {
		super();
		System.out.println("MessageService...");
	}

	/**
	 * print Hello World
	 * @return print Hello World
	 */
	public String getMessage() {
		return "Hello World";
	}
}

```

## Spring Autowired 方式(xml)

傳統 XML 配置

```java
package hello;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.ClassPathXmlApplicationContext;


public class ApplicationSpring {

	public static void main(String[] args) {

		System.out.println("applicationSpring");

		ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

		// 從容器中獲取 MessagePrinter 物件
		MessagePrinter printer = context.getBean(MessagePrinter.class);

		System.out.println(printer);

		printer.printMessage();
	}
}

```

其他的 Java 就把所有 @xxx 拿掉

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd 
    http://www.springframework.org/schema/context 
    http://www.springframework.org/schema/context/spring-context.xsd">
     
    <!--
        bean 元素: 描述當前的物件需要由 spring 容器管理
        id 屬性: 標示物件，未來在應用程序中可以根據 id 獲取物件
        class: 被管理物件的類全名
    -->
    <bean id="service"    class="hello.MessageService"></bean>
    <!--
        <property name="service" ...
        這邊的 name 是指 MessagePrinter.java
        	private MessageService service;

        ref="service"
        這邊的 service 是指上面的
        <bean id="service"    class="hello.MessageService"></bean>
    -->
    <bean id="printer"    class="hello.MessagePrinter">
        <property name="service" ref="service">
    </bean>
     
</beans> 

```

Application

### Eclipse 建置 applicationContext.xml

[eclipse 新建applicationContext.xml文件 - 开发者知识库](https://www.itdaan.com/tw/aca117e170d18e2da5fd791876dfece2)

目前只找到複製方式貼上...

```xml applicationContext.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd 
    http://www.springframework.org/schema/context 
    http://www.springframework.org/schema/context/spring-context.xsd">
     
     
</beans> 
```

這樣就能正常執行了

### Resource leak: context is never closed

- [警告：Spring ApplicationContext - Resource leak: context is never closed的处理_Java_梅森上校的博客 业精于勤荒于嬉，形成于思毁于随。-CSDN博客](https://blog.csdn.net/seagal890/article/details/53958868)
- [spring - How does this code resolve "Resource Leak:'context' is never closed" warning? - Stack Overflow](https://stackoverflow.com/questions/26573767/how-does-this-code-resolve-resource-leakcontext-is-never-closed-warning)
```
((ConfigurableApplicationContext) context).close();
```


## Spring 查看 log4j IoC 過程

Spring 使用 common-logging ，所以看一下關方文件說明
> To make Log4j 1.2 work with the default JCL dependency (commons-logging) all you need to do is put Log4j on the classpath, and provide it with a configuration file (log4j.properties or log4j.xml in the root of the classpath)

[2. Introduction to the Spring Framework](https://docs.spring.io/spring/docs/4.3.13.RELEASE/spring-framework-reference/html/overview.html#overview-logging-log4j)

common-logging 跟 slf4j 差別
[java日志组件介绍（common-logging，log4j，slf4j，logback ）_Java_yycdaizi的专栏-CSDN博客](https://blog.csdn.net/yycdaizi/article/details/8276265)

> slf4j 與 common-logging 比較
common-logging通過動態查找的機制，在程序運行時自動找出真正使用的日誌庫。由於它使用了ClassLoader尋找和載入底層的日誌庫， 導致了象OSGI這樣的框架無法正常工作，因為OSGI的不同的插件使用自己的ClassLoader。 OSGI的這種機制保證了插件互相獨立，然而卻使Apache Common-Logging無法工作。
> slf4j在編譯時靜態綁定真正的Log庫,因此可以再OSGI中使用。另外，SLF4J 支持參數化的log字符串，避免了之前為了減少字符串拼接的性能損耗而不得不寫的if(logger.isDebugEnable())，現在你可以直接寫：logger.debug(「current user is: {}」, user)。拼裝消息被推遲到了它能夠確定是不是要顯示這條消息的時候，但是獲取參數的代價並沒有倖免。

```xml 
	<dependency>
		<groupId>log4j</groupId>
		<artifactId>log4j</artifactId>
		<version>1.2.17</version>
	</dependency>
```

```properties log4j.properties
# info 級別日誌，名子叫 stdout
log4j.rootCategory=INFO, stdout

# 輸出到控制台上輸出
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
# 按照一定格式輸出
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %t %c{2}:%L - %m%n
# spring factory debug 級別輸出
log4j.category.org.springframework.beans.factory=DEBUG
```

不同 log (SLF4J) 設定可以看[2. Introduction to the Spring Framework](https://docs.spring.io/spring/docs/4.3.13.RELEASE/spring-framework-reference/html/overview.html#overview-logging-log4j)

這實候執行程式

可以看到以下 log
```
applicationSpring
16:44:28,186  INFO main support.ClassPathXmlApplicationContext:583 - Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@2a18f23c: startup date [Sat May 02 16:44:28 CST 2020]; root of context hierarchy
16:44:28,220  INFO main xml.XmlBeanDefinitionReader:317 - Loading XML bean definitions from class path resource [applicationContext.xml]
16:44:28,242 DEBUG main xml.DefaultDocumentLoader:73 - Using JAXP provider [com.sun.org.apache.xerces.internal.jaxp.DocumentBuilderFactoryImpl]
16:44:28,266 DEBUG main xml.PluggableSchemaResolver:141 - Loading schema mappings from [META-INF/spring.schemas]
16:44:28,270 DEBUG main xml.PluggableSchemaResolver:147 - Loaded schema mappings: {http://www.springframework.org/schema/context/spring-context-3.2.xsd=org/springframework/context/config/spring-context-3.2.xsd, http://www.springframework.org/schema/cache/spring-cache-4.3.xsd=org/springframework/cache/config/spring-cache-4.3.xsd, http://www.springframework.org/schema/beans/spring-beans-4.0.xsd=org/springframework/beans/factory/xml/spring-beans-4.0.xsd, http://www.springframework.org/schema/context/spring-context-2.5.xsd=org/springframework/context/config/spring-context-2.5.xsd, http://www.springframework.org/schema/context/spring-context-4.0.xsd=org/springframework/context/config/spring-context-4.0.xsd, http://www.springframework.org/schema/util/spring-util-4.3.xsd=org/springframework/beans/factory/xml/spring-util-4.3.xsd, http://www.springframework.org/schema/jee/spring-jee-2.0.xsd=org/springframework/ejb/config/spring-jee-2.0.xsd, http://www.springframework.org/schema/aop/spring-aop-2.0.xsd=org/springframework/aop/config/spring-aop-2.0.xsd, http://www.springframework.org/schema/tool/spring-tool-4.3.xsd=org/springframework/beans/factory/xml/spring-tool-4.3.xsd, http://www.springframework.org/schema/util/spring-util-2.0.xsd=org/springframework/beans/factory/xml/spring-util-2.0.xsd, http://www.springframework.org/schema/cache/spring-cache-4.2.xsd=org/springframework/cache/config/spring-cache-4.2.xsd, http://www.springframework.org/schema/context/spring-context-3.1.xsd=org/springframework/context/config/spring-context-3.1.xsd, http://www.springframework.org/schema/tool/spring-tool-2.0.xsd=org/springframework/beans/factory/xml/spring-tool-2.0.xsd, http://www.springframework.org/schema/tool/spring-tool.xsd=org/springframework/beans/factory/xml/spring-tool-4.3.xsd, http://www.springframework.org/schema/beans/spring-beans-3.2.xsd=org/springframework/beans/factory/xml/spring-beans-3.2.xsd, http://www.springframework.org/schema/util/spring-util-4.2.xsd=org/springframework/beans/factory/xml/spring-util-4.2.xsd, http://www.springframework.org/schema/beans/spring-beans-2.5.xsd=org/springframework/beans/factory/xml/spring-beans-2.5.xsd, http://www.springframework.org/schema/beans/spring-beans.xsd=org/springframework/beans/factory/xml/spring-beans-4.3.xsd, http://www.springframework.org/schema/tool/spring-tool-4.0.xsd=org/springframework/beans/factory/xml/spring-tool-4.0.xsd, http://www.springframework.org/schema/util/spring-util-4.1.xsd=org/springframework/beans/factory/xml/spring-util-4.1.xsd, http://www.springframework.org/schema/beans/spring-beans-4.2.xsd=org/springframework/beans/factory/xml/spring-beans-4.2.xsd, http://www.springframework.org/schema/context/spring-context-4.2.xsd=org/springframework/context/config/spring-context-4.2.xsd, http://www.springframework.org/schema/cache/spring-cache-4.1.xsd=org/springframework/cache/config/spring-cache-4.1.xsd, http://www.springframework.org/schema/lang/spring-lang-3.0.xsd=org/springframework/scripting/config/spring-lang-3.0.xsd, http://www.springframework.org/schema/jee/spring-jee-4.3.xsd=org/springframework/ejb/config/spring-jee-4.3.xsd, http://www.springframework.org/schema/aop/spring-aop-4.3.xsd=org/springframework/aop/config/spring-aop-4.3.xsd, http://www.springframework.org/schema/beans/spring-beans-4.3.xsd=org/springframework/beans/factory/xml/spring-beans-4.3.xsd, http://www.springframework.org/schema/tool/spring-tool-4.1.xsd=org/springframework/beans/factory/xml/spring-tool-4.1.xsd, http://www.springframework.org/schema/task/spring-task-4.3.xsd=org/springframework/scheduling/config/spring-task-4.3.xsd, http://www.springframework.org/schema/util/spring-util-4.0.xsd=org/springframework/beans/factory/xml/spring-util-4.0.xsd, http://www.springframework.org/schema/beans/spring-beans-4.1.xsd=org/springframework/beans/factory/xml/spring-beans-4.1.xsd, http://www.springframework.org/schema/lang/spring-lang.xsd=org/springframework/scripting/config/spring-lang-4.3.xsd, http://www.springframework.org/schema/cache/spring-cache-4.0.xsd=org/springframework/cache/config/spring-cache-4.0.xsd, http://www.springframework.org/schema/context/spring-context-4.1.xsd=org/springframework/context/config/spring-context-4.1.xsd, http://www.springframework.org/schema/aop/spring-aop-4.2.xsd=org/springframework/aop/config/spring-aop-4.2.xsd, http://www.springframework.org/schema/tool/spring-tool-4.2.xsd=org/springframework/beans/factory/xml/spring-tool-4.2.xsd, http://www.springframework.org/schema/util/spring-util-2.5.xsd=org/springframework/beans/factory/xml/spring-util-2.5.xsd, http://www.springframework.org/schema/jee/spring-jee-4.2.xsd=org/springframework/ejb/config/spring-jee-4.2.xsd, http://www.springframework.org/schema/lang/spring-lang-3.1.xsd=org/springframework/scripting/config/spring-lang-3.1.xsd, http://www.springframework.org/schema/task/spring-task.xsd=org/springframework/scheduling/config/spring-task-4.3.xsd, http://www.springframework.org/schema/task/spring-task-4.2.xsd=org/springframework/scheduling/config/spring-task-4.2.xsd, http://www.springframework.org/schema/aop/spring-aop.xsd=org/springframework/aop/config/spring-aop-4.3.xsd, http://www.springframework.org/schema/lang/spring-lang-4.0.xsd=org/springframework/scripting/config/spring-lang-4.0.xsd, http://www.springframework.org/schema/beans/spring-beans-2.0.xsd=org/springframework/beans/factory/xml/spring-beans-2.0.xsd, http://www.springframework.org/schema/task/spring-task-4.1.xsd=org/springframework/scheduling/config/spring-task-4.1.xsd, http://www.springframework.org/schema/tool/spring-tool-3.1.xsd=org/springframework/beans/factory/xml/spring-tool-3.1.xsd, http://www.springframework.org/schema/util/spring-util-3.2.xsd=org/springframework/beans/factory/xml/spring-util-3.2.xsd, http://www.springframework.org/schema/jee/spring-jee-4.1.xsd=org/springframework/ejb/config/spring-jee-4.1.xsd, http://www.springframework.org/schema/lang/spring-lang-3.2.xsd=org/springframework/scripting/config/spring-lang-3.2.xsd, http://www.springframework.org/schema/cache/spring-cache-3.2.xsd=org/springframework/cache/config/spring-cache-3.2.xsd, http://www.springframework.org/schema/aop/spring-aop-4.1.xsd=org/springframework/aop/config/spring-aop-4.1.xsd, http://www.springframework.org/schema/util/spring-util.xsd=org/springframework/beans/factory/xml/spring-util-4.3.xsd, http://www.springframework.org/schema/jee/spring-jee-3.2.xsd=org/springframework/ejb/config/spring-jee-3.2.xsd, http://www.springframework.org/schema/util/spring-util-3.0.xsd=org/springframework/beans/factory/xml/spring-util-3.0.xsd, http://www.springframework.org/schema/lang/spring-lang-4.1.xsd=org/springframework/scripting/config/spring-lang-4.1.xsd, http://www.springframework.org/schema/aop/spring-aop-3.2.xsd=org/springframework/aop/config/spring-aop-3.2.xsd, http://www.springframework.org/schema/tool/spring-tool-3.2.xsd=org/springframework/beans/factory/xml/spring-tool-3.2.xsd, http://www.springframework.org/schema/context/spring-context-4.3.xsd=org/springframework/context/config/spring-context-4.3.xsd, http://www.springframework.org/schema/lang/spring-lang-2.5.xsd=org/springframework/scripting/config/spring-lang-2.5.xsd, http://www.springframework.org/schema/jee/spring-jee.xsd=org/springframework/ejb/config/spring-jee-4.3.xsd, http://www.springframework.org/schema/util/spring-util-3.1.xsd=org/springframework/beans/factory/xml/spring-util-3.1.xsd, http://www.springframework.org/schema/jee/spring-jee-2.5.xsd=org/springframework/ejb/config/spring-jee-2.5.xsd, http://www.springframework.org/schema/task/spring-task-4.0.xsd=org/springframework/scheduling/config/spring-task-4.0.xsd, http://www.springframework.org/schema/aop/spring-aop-2.5.xsd=org/springframework/aop/config/spring-aop-2.5.xsd, http://www.springframework.org/schema/context/spring-context.xsd=org/springframework/context/config/spring-context-4.3.xsd, http://www.springframework.org/schema/cache/spring-cache-3.1.xsd=org/springframework/cache/config/spring-cache-3.1.xsd, http://www.springframework.org/schema/aop/spring-aop-4.0.xsd=org/springframework/aop/config/spring-aop-4.0.xsd, http://www.springframework.org/schema/jee/spring-jee-4.0.xsd=org/springframework/ejb/config/spring-jee-4.0.xsd, http://www.springframework.org/schema/tool/spring-tool-2.5.xsd=org/springframework/beans/factory/xml/spring-tool-2.5.xsd, http://www.springframework.org/schema/task/spring-task-3.2.xsd=org/springframework/scheduling/config/spring-task-3.2.xsd, http://www.springframework.org/schema/beans/spring-beans-3.0.xsd=org/springframework/beans/factory/xml/spring-beans-3.0.xsd, http://www.springframework.org/schema/jee/spring-jee-3.0.xsd=org/springframework/ejb/config/spring-jee-3.0.xsd, http://www.springframework.org/schema/aop/spring-aop-3.1.xsd=org/springframework/aop/config/spring-aop-3.1.xsd, http://www.springframework.org/schema/jee/spring-jee-3.1.xsd=org/springframework/ejb/config/spring-jee-3.1.xsd, http://www.springframework.org/schema/lang/spring-lang-4.3.xsd=org/springframework/scripting/config/spring-lang-4.3.xsd, http://www.springframework.org/schema/aop/spring-aop-3.0.xsd=org/springframework/aop/config/spring-aop-3.0.xsd, http://www.springframework.org/schema/beans/spring-beans-3.1.xsd=org/springframework/beans/factory/xml/spring-beans-3.1.xsd, http://www.springframework.org/schema/lang/spring-lang-2.0.xsd=org/springframework/scripting/config/spring-lang-2.0.xsd, http://www.springframework.org/schema/task/spring-task-3.1.xsd=org/springframework/scheduling/config/spring-task-3.1.xsd, http://www.springframework.org/schema/lang/spring-lang-4.2.xsd=org/springframework/scripting/config/spring-lang-4.2.xsd, http://www.springframework.org/schema/tool/spring-tool-3.0.xsd=org/springframework/beans/factory/xml/spring-tool-3.0.xsd, http://www.springframework.org/schema/task/spring-task-3.0.xsd=org/springframework/scheduling/config/spring-task-3.0.xsd, http://www.springframework.org/schema/context/spring-context-3.0.xsd=org/springframework/context/config/spring-context-3.0.xsd, http://www.springframework.org/schema/cache/spring-cache.xsd=org/springframework/cache/config/spring-cache-4.3.xsd}
16:44:28,272 DEBUG main xml.PluggableSchemaResolver:119 - Found XML schema [http://www.springframework.org/schema/beans/spring-beans.xsd] in classpath: org/springframework/beans/factory/xml/spring-beans-4.3.xsd
16:44:28,303 DEBUG main xml.DefaultBeanDefinitionDocumentReader:92 - Loading bean definitions
16:44:28,312 DEBUG main xml.XmlBeanDefinitionReader:224 - Loaded 2 bean definitions from location pattern [applicationContext.xml]
16:44:28,329 DEBUG main support.DefaultListableBeanFactory:730 - Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@36f0f1be: defining beans [service,printer]; root of factory hierarchy
16:44:28,330 DEBUG main support.DefaultListableBeanFactory:221 - Creating shared instance of singleton bean 'service'
16:44:28,330 DEBUG main support.DefaultListableBeanFactory:449 - Creating instance of bean 'service'
MessageService...
16:44:28,337 DEBUG main support.DefaultListableBeanFactory:539 - Eagerly caching bean 'service' to allow for resolving potential circular references
16:44:28,338 DEBUG main support.DefaultListableBeanFactory:485 - Finished creating instance of bean 'service'
16:44:28,338 DEBUG main support.DefaultListableBeanFactory:221 - Creating shared instance of singleton bean 'printer'
16:44:28,338 DEBUG main support.DefaultListableBeanFactory:449 - Creating instance of bean 'printer'
MessagePrinter...
16:44:28,338 DEBUG main support.DefaultListableBeanFactory:539 - Eagerly caching bean 'printer' to allow for resolving potential circular references
16:44:28,339 DEBUG main support.DefaultListableBeanFactory:251 - Returning cached instance of singleton bean 'service'
16:44:28,350 DEBUG main support.DefaultListableBeanFactory:485 - Finished creating instance of bean 'printer'
16:44:28,351 DEBUG main support.DefaultListableBeanFactory:251 - Returning cached instance of singleton bean 'lifecycleProcessor'
16:44:28,353 DEBUG main support.DefaultListableBeanFactory:251 - Returning cached instance of singleton bean 'printer'
hello.MessagePrinter@61230f6a
Hello World
```

簡單重點 log 說明

1. Using JAXP provider 使用 JAXP 解析 XML

2. Loading bean definitions  加載 bean 節點

3. Creating shared instance of singleton bean 'service' 使用單立模式新增 service 物件

意思程式執行時候，會把所有 bean 實例出來!!

### log4j:WARN No appenders could be found for logger (org.springframework.core.env.StandardEnvironment).
出現這個代表 log設定檔放錯地方

src\main\resources\log4j.properties


## autowired 加載方式

TODO

#### 建構子

#### 成員變數

#### setter method

#### 任意 method