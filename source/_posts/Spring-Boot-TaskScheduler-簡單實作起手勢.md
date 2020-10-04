---
title: Spring Boot TaskScheduler 簡單實作起手勢
date: 2020-09-14 20:22:22
tags: [spring, taskscheduler]
categories: Java
---

最近看到 Spring Boot TaskScheduler 簡單的範例
就簡單實作
沒想到這麼簡單

<!--more-->

## 簡單實作


1.  @EnableScheduling
在 @SpringBootApplication 下面配置 @EnableScheduling

2.  配置 Task 類別

```java
@Component
public class ScheduledTask {

    private static final SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    private Integer count0 = 1;
    private Integer count1 = 1;
    private Integer count2 = 1;

    @Scheduled(fixedRate = 5000)
    public void reportCurrentTime() throws InterruptedException {
        System.out.println(String.format("---第%s次執行，當前時間為：%s", count0++, dateFormat.format(new Date())));
    }

    @Scheduled(fixedDelay = 5000)
    public void reportCurrentTimeAfterSleep() throws InterruptedException {
        System.out.println(String.format("===第%s次執行，當前時間為：%s", count1++, dateFormat.format(new Date())));
    }

    @Scheduled(cron = "0 0 1 * * *")
    public void reportCurrentTimeCron() throws InterruptedException {
        System.out.println(String.format("+++第%s次執行，當前時間為：%s", count2++, dateFormat.format(new Date())));
    }

}
```
參考: [基於Spring Boot 實現定時任務-springboot,java,spring 相關文章-天碼營](https://www.tianmaying.com/tutorial/spring-scheduling-task)

這邊有趣的是 cron 這邊有到秒
然後這邊程式是用單線程
所以前一個超過時間就不會執行(我看到文章是這樣寫)
這邊就不敘述，因為我也不是很懂

## 相關文章

* [27. Task Execution and Scheduling](https://docs.spring.io/spring/docs/3.2.x/spring-framework-reference/html/scheduling.html)
* [【小家Spring】Spring任务调度@Scheduled的使用以及原理、源码分析（@EnableScheduling）_YourBatman-CSDN博客](https://blog.csdn.net/f641385712/article/details/89442241)
* [Spring Scheduler的使用与坑 | 懒程序员改变世界](http://qinghua.github.io/spring-scheduler/)
* [SpringBoot中使用任务调度器的注意事项](http://betterlxc.com/2017/09/SpringBoot%E4%B8%AD%E4%BD%BF%E7%94%A8%E4%BB%BB%E5%8A%A1%E8%B0%83%E5%BA%A6%E5%99%A8%E7%9A%84%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9/)
* [Spring Schedule Tutorial · Burak Aktas](http://buraktas.com/spring-schedule-tutorial/)
* [How to Schedule Tasks with Spring Boot | CalliCoder](https://www.callicoder.com/spring-boot-task-scheduling-with-scheduled-annotation/)
* [java - Spring Boot Shutdown with Scheduler - Stack Overflow](https://stackoverflow.com/questions/41903782/spring-boot-shutdown-with-scheduler)
* [基於Spring Boot 實現定時任務-springboot,java,spring 相關文章-天碼營](https://www.tianmaying.com/tutorial/spring-scheduling-task)
* [分布式任务调度平台XXL-JOB](https://www.xuxueli.com/xxl-job/#5.4.1%20quartz%E7%9A%84%E4%B8%8D%E8%B6%B3)
* [在Spring Boot中优雅的实现定时任务 - 知乎](https://zhuanlan.zhihu.com/p/79644891)
* [数据批处理神器-Spring Batch(1)简介及使用场景 | mason's blog](https://mianshenglee.github.io/2019/06/04/springbatch(1).html)
* [动态改变Spring定时任务执行频率-阿里云开发者社区](https://developer.aliyun.com/article/488617)
* [Springboot定时任务踩坑记录 - 技术杂谈 - SegmentFault 思否](https://segmentfault.com/a/1190000018707820)
* [Shutdown a Spring Boot Application | Baeldung](https://www.baeldung.com/spring-boot-shutdown)

Spring Batch 跟 Spring Boot Task 是不一樣東西...


## 問題

我在想程是執行中那要怎麼關閉程式呢?
我想改一個方法就要把所有程式關掉
這樣執行中程式不是就會有問題

不過，也有可能 Spring Boot Task 應該只要放一個任務工作程式就沒這個問題?
但實務上是不是這樣做就不知道了
