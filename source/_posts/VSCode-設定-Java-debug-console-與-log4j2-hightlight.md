---
title: VSCode 設定 Java debug console 與 log4j2 hightlight
date: 2022-07-31 00:43:41
tags: [vscode,log4j2]
categories: Java
---

最近用接的專案有用 maven，我發現可以在 VSCODE 開發成功，除了採到 Spring Boot `MultipartFile.transferTo`雷。不過修完後就沒有什麼問題。


<!--more-->

## 設定 VScode Debug Console

主要加`"console": "internalConsole",`可以參考下面設定。

```java=
        {
            "type": "java",
            "name": "Launch springApplication",
            "request": "launch",
            "mainClass": "com.xxxx.spring.web.springApplication",
            "projectName": "spring_web",
            "cwd": "${workspaceFolder}/spring_web",
            "console": "internalConsole",
        },
        {
```

設定完執行程式可以看到下圖。但我覺得好像不太好看資訊。
![](https://i.imgur.com/3FuK6NR.png)


## Spring Boot 設定 Console 顏色

application.properties設定
```
spring.output.ansi.enabled=always
```
參考:[菜鳥工程師 肉豬: Spring Boot 操控台印出彩色文字 console log ANSI color output](https://matthung0807.blogspot.com/2020/09/spring-boot-console-log-ansi-color-output.html)

但只有變文字...，這不是我想要的。
![](https://i.imgur.com/djU3uYT.png)


## 設定 log4j highlight 顏色

log4j2.xml
```xml=
<Configuration>
    <Properties>
        <property name="LOG_HOME" value="/PPKIS/logs" />
		<property name="LOG_PREFIX" value="ppkis" />
		<property name="LOG_PATTERN" value="%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n" />
        <property name="LOG_FORMAT_CONSOLE">%d{yyyy-MM-dd HH:mm:ss.SSS} %highlight{%-5level} [%t] %highlight{%c{1.}.%M(%L)}: %msg%n%throwable</property>
		<property name="KEEP_DAYS" value="30d" />
    </Properties>

    <Appenders>
        <Console name="ConsoleAppender" target="SYSTEM_OUT" follow="true">        	
            <PatternLayout pattern="${LOG_FORMAT_CONSOLE}" disableAnsi="false" noConsoleNoAnsi="false"/>
        </Console>

        <RollingFile name="FileAppender" fileName="${LOG_HOME}/${LOG_PREFIX}.log" filePattern="${LOG_HOME}/${LOG_PREFIX}-%d{yyyy-MM-dd}-%i.log">
```

![](https://i.imgur.com/WufrU1o.png)


人生充滿光明。:D

參考:
- [springboot log4j2配置参考-highlight-IDE控制台彩色日志_绿林__的博客-CSDN博客](https://blog.csdn.net/dixialieren/article/details/106398841)
- [(11) Spring Boot 使用 Logback 或 Log4j2 | by Albert Hg | learning-from-jhipster | Medium](https://medium.com/learning-from-jhipster/11-spring-boot-%E4%BD%BF%E7%94%A8-logback-%E6%88%96-log4j2-e655b320a2c8)
- [springboot log4j2配置参考-highlight-IDE控制台彩色日志 - CodeAntenna](https://codeantenna.com/a/rULTMlSWMR)