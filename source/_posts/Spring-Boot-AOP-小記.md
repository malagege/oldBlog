---
title: Spring Boot AOP 小記
date: 2022-07-31 01:21:08
tags: [spring,aop]
categories: Java
---
之前我同事推坑 `Spring Boot` 框架給我，跟我說 `Sping` 重要觀念有兩個，一個是 `注入` 另一個是 `AOP` 。目前注入常常使用，但是不常使用 AOP，最近用點時間整理一下。


<!--more-->

## 觀念

### 推薦

- [轻松理解 Spring AOP - 阿dun - 博客园](https://www.cnblogs.com/aduner/p/14656427.html#%E7%AC%AC%E4%B8%80%E6%AD%A5%EF%BC%9A%E9%80%89%E6%8B%A9%E8%BF%9E%E6%8E%A5%E7%82%B9) [備份圖](https://imgur.com/H4BfOAa.jpeg)

- [Spring AOP概念Aspect、Advice、JoinPoint、JoinCut与Execution_Anur的博客-CSDN博客_advice aspect](https://blog.csdn.net/anurnomeru/article/details/79798659)
可以看詳細

- [Introduction to Spring AOP | Baeldung](https://www.baeldung.com/spring-aop)

- [浅谈AOP以及AspectJ和Spring AOP - SegmentFault 思否](https://segmentfault.com/a/1190000020621578)

- [徹底征服 Spring AOP 之 理論篇 - 簡書](https://www.jianshu.com/p/27cc6a3bcab6) [備份圖](https://imgur.com/fkM3fZS)
- [徹底征服 Spring AOP 之 實戰篇 - 簡書](https://www.jianshu.com/p/7c1a674ba42f)

[AspectJ报错：error at ::0 can't find referenced pointcut XXX - 楼兰胡杨 - 博客园](https://www.cnblogs.com/east7/p/9991257.html)

 
[菜鳥工程師 肉豬: Spring AOP get method advice annotation and parameters](https://matthung0807.blogspot.com/2020/06/spring-aop-get-method-advice-annotation.html)

[4.3.3　处理通知中的参数 - Spring 实战(第四版)](https://potoyang.gitbook.io/spring-in-action-v4/untitled/untitled-3/untitled-2)

## AOP錯誤: error at ::0 can't find referenced pointcut XXX

```java=
package com.example.demo;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;
import org.springframework.util.StopWatch;

@Component
@Aspect
public class TestAspect {
    ....

    @Around("com.example.demo.TestClass.callMethod()")
    public Object doAroundAccessCheck(ProceedingJoinPoint pjp) throws Throwable {
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();
        // 开始
        Object retVal = pjp.proceed();
        stopWatch.stop();
        // 结束
        System.out.println("invoke method: " + pjp.getSignature().getName() + ", elapsed time: " + stopWatch.getTotalTimeMillis());
        return retVal;
    }
}

```

可參考:[AspectJ报错：error at ::0 can't find referenced pointcut XXX - 楼兰胡杨 - 博客园](https://www.cnblogs.com/east7/p/9991257.html)
![](https://i.imgur.com/axe7Is2.png)

不過我參照上面方法都失敗，所以我後來嘗試加上`execution`，順利解決問題。

```java=
    @Around("execution(* com.example.demo.TestClass.callMethod())")
    public Object doAroundAccessCheck(ProceedingJoinPoint pjp) throws Throwable {
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();
        // 开始
        Object retVal = pjp.proceed();
        stopWatch.stop();
        // 结束
        System.out.println("invoke method: " + pjp.getSignature().getName() + ", elapsed time: " + stopWatch.getTotalTimeMillis());
        return retVal;
    }
```

### 名詞小記

![](https://i.imgur.com/yAKJwxs.png)
[框架源码系列十：Spring AOP（AOP的核心概念回顾、Spring中AOP的用法、Spring AOP 源码学习） - 小不点啊 - 博客园](https://www.cnblogs.com/leeSmall/p/10236553.html)

解說名詞:
- [淺談AOP以及AspectJ和Spring AOP - SegmentFault 思否](https://segmentfault.com/a/1190000020621578)
- [Spring系列（四）：Spring AOP详解 - toby.xu - 博客园](https://www.cnblogs.com/toby-xu/p/11361351.html)
- [彻底征服 Spring AOP 之 理论篇 - 简书](https://www.jianshu.com/p/27cc6a3bcab6)
- [Spring AOP概念Aspect、Advice、JoinPoint、JoinCut与Execution_Anur的博客-CSDN博客_advice aspect](https://blog.csdn.net/anurnomeru/article/details/79798659)


其實當初看敘述有看沒有懂，對照敘述我的理解先記錄下來，之後回頭複習希望能馬上進入狀況。

簡單我的觀點來看，Spring `@Transactional`都會用代理模式，這邊AOP其實也類似做一樣動作，底層(應該)用做`代理模式`去做，達成這個效果。

- JoinPoint: 中文叫`連接點`，多個執行點可執行自訂要跑的程式(Advice)。
- PointCut: 中文叫`斷點`，其中一個被選定的JoinPoint。
- Advice: 就是你當PointCut 觸發要執行的程式。

程式配置可以參考:
![](https://i.imgur.com/X5sC4oK.jpg)


## 記錄重點

### pom.xml

不知道為什麼 Spring Initializr 找不到這個，要自己加到 `pom.xml`。


```xml=
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

### 程式

src/main/java/com/example/demo/DemoApplication.java
```java=
@SpringBootApplication
public class DemoApplication {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(DemoApplication.class, args);
		var testClass = ctx.getBean(TestClass.class);
		testClass.callMethod();
	}

}

```

src/main/java/com/example/demo/TestClass.java
```java=
package com.example.demo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class TestClass {

    @Autowired
    TestClass _testClass;

    public void callMethod() {
        System.out.println("Hello World!!!!你好世界");
        _testClass.callMethod2();
    }


    public void callMethod2() {
        System.out.println(" callMethod2");
    }

}

```

src/main/java/com/example/demo/TestAspect.java
```java=
@Component
@Aspect
public class TestAspect {
    @Before("execution(* callMethod())")
    public void before(){
        System.out.println("我在你前面做");
    }

    @After("execution(* callMethod())")
    public void after(){
        System.out.println("我在你後面做");
    }

    @Around("execution(* com.example.demo.TestClass.callMethod())")
    public Object doAroundAccessCheck(ProceedingJoinPoint pjp) throws Throwable {
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();
        // 开始
        Object retVal = pjp.proceed();
        stopWatch.stop();
        // 结束
        System.out.println("invoke method: " + pjp.getSignature().getName() + ", elapsed time: " + stopWatch.getTotalTimeMillis());
        return retVal;
    }
}
```

![](https://i.imgur.com/D5diOgP.png)

## 其他重點

![](https://i.imgur.com/RsDFE3p.png)

![](https://i.imgur.com/5r14L8C.png)

參考:[Spring AOP中JoinPoint的用法 - 简书](https://www.jianshu.com/p/90881bfc3241)

Object[] getArgs();	獲取傳入目標方法的引數物件
Object getTarget();	獲取被代理的物件
Object getThis();	獲取代理物件


![](https://i.imgur.com/FAFbPAK.png)
參考:[Spring AOP概念Aspect、Advice、JoinPoint、JoinCut與Execution_Anur的博客-CSDN博客_advice aspect](https://blog.csdn.net/anurnomeru/article/details/79798659)

![](https://i.imgur.com/767mPE7.png)
參考:[4.3.3　处理通知中的参数 - Spring 实战(第四版)](https://potoyang.gitbook.io/spring-in-action-v4/untitled/untitled-3/untitled-2)


## 感想

這邊源碼我有丟到 Github: [malagege/spring-boot-aop-example: 範例測試](https://github.com/malagege/spring-boot-aop-example)

我以為很難，其實寫出範例又好像很好。後來想到滿多機制能用這個寫，像重試API測試，我們也可以透過AOP方式，後來我很好奇有沒有別人現成寫好的工具，發現`spring-retry`可以做到這些事情。

文章整理:
- [spring-retry 重試機制](https://www.tpisoftware.com/tpu/articleDetails/1407)
- [Spring Boot中使用Spring-Retry重试框架_c.的博客-CSDN博客_springboot 重试框架](https://blog.csdn.net/cckevincyh/article/details/112347200)
- [Spring 指南（spring-retry） - SegmentFault 思否](https://segmentfault.com/a/1190000019932970)


## 彩蛋

依賴注入循環問題。

```
spring.main.allow-circular-references=true
```

- [Requested bean is currently in creation: Is there an unresolvable circular reference?_Moshow郑锴的博客-CSDN博客](https://blog.csdn.net/moshowgame/article/details/124086253)

- [基于Spring AOP的分布式锁简单实现 | Smart_咚咚](https://dongbow.github.io/2019/07/23/dislock/)

- [基于AOP实现简单的自动重复执行注解 - SZnCu的喵窝](https://www.szncu.club/index.php/archives/355/)

### .Net Core 另外 Retry 方案

- [HttpClient，該 using 還是 static?-黑暗執行緒](https://blog.darkthread.net/blog/httpclient-sigleton/)
- [Using Retry Pattern In ASP.NET Core Via Polly](https://www.c-sharpcorner.com/article/using-retry-pattern-in-asp-net-core-via-polly/)
- [.NET 开源项目 Polly 介绍 - 精致码农 - 博客园](https://www.cnblogs.com/willick/p/polly.html)