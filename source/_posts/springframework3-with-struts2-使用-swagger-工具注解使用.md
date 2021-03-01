---
title: springframework3 with struts2 使用 swagger 工具注解使用
date: 2021-01-04 23:01:51
tags: 
categories: Java
---

搭在 spring 3 網路上很多教學都是使用 spring MVC 去做串接
由於公司上使用是舊版
所以找找有沒有能用的方法
最後成功來記錄一下

<!--more-->

## 下載依賴

```xml
<dependency>
    <groupId>io.swagger</groupId>
    <artifactId>swagger-jersey2-jaxrs</artifactId> 
    <version>1.5.0</version>
</dependency>
```

由於我專案沒有用 maven
所以...自己想辦法抓

```
mvn dependency:get -DgroupId=io.swagger -DartifactId=swagger-jersey2-jaxrs -Dversion=1.6.2 -Ddest=/mnt/d/mvntest
```

參考:[How to simply download a JAR using Maven? - Stack Overflow](https://stackoverflow.com/questions/7110114/how-to-simply-download-a-jar-using-maven)

但後來發現還是不比[Maven 建置專案初體驗 | 程式狂想筆記](https://malagege.github.io/blog/2020/06/15/Maven-%E5%BB%BA%E7%BD%AE%E5%B0%88%E6%A1%88%E5%88%9D%E9%AB%94%E9%A9%97/#%E4%B8%80%E7%A8%AE%E5%BF%AB%E9%80%9F%E8%BC%B8%E5%85%A5%E5%BB%BA%E7%BD%AE%E6%96%B9%E6%B3%95)設定好 pom.xml 在執行`mvn dependency:copy-dependencies`快速



## 配製 swagger(web.xml)

以下參照[Jersey整合Swagger自动生成API文档](https://blog.devzeng.com/blog/jersey-swagger-api.html)

1. 可依照源專案 servlet 還是filter 做調整

```xml 
<!-- servlet 設定-->
<!-- jersey -->
<servlet>
    <servlet-name>jersey</servlet-name>
    <servlet-class>org.glassfish.jersey.servlet.ServletContainer</servlet-class>
    <init-param>
        <param-name>jersey.config.server.provider.packages</param-name>
        <param-value>io.swagger.jaxrs.listing,com.devzeng.service.schedule.api</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>jersey</servlet-name>
    <url-pattern>/api/*</url-pattern>
</servlet-mapping>
```

2. 新增swagger配置

```xml
<!-- swagger -->
<servlet>
    <servlet-name>Jersey2Config</servlet-name>
    <servlet-class>io.swagger.jersey.config.JerseyJaxrsConfig</servlet-class>
    <init-param>
        <param-name>api.version</param-name>
        <param-value>1.0.0</param-value>
    </init-param>
    <init-param>
        <param-name>swagger.api.basepath</param-name>
        <param-value>http://localhost:8080/api</param-value>
    </init-param>
    <load-on-startup>2</load-on-startup>
</servlet>
```

說明：

    swagger.api.basepath:這個是api訪問的baseurl


## 配置前端 swagger UI

```bash
git clone  https://github.com/swagger-api/swagger-ui.git
git checkout 2.x # 由於版本過舊，建議使用這個版本
```

> 4、配置swagger-ui
> (1) 下載swagger-ui
> 
> https://github.com/swagger-api/swagger-ui
> 
> 推薦使用2.x版本
> (2) 拷貝dist目錄下面的文件到webroot下面
> (3) 修改index.html頁面的url地址
> 
> url = "http://localhost:8080/api/swagger.json";


## 其他文章

[Documenting RESTful Java Web Services using Swagger](https://hub.packtpub.com/restful-java-web-services-swagger/)

[1: Spring Boot 使用JSONDoc快速生成Restful API - 简书](https://www.jianshu.com/p/291217345e5d)
