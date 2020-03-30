---
title: Java 雜記
date: 2020-03-28 20:24:13
tags:
categories:
---


<!--more-->

## spring core

[開源框架: Spring Gossip](https://openhome.cc/Gossip/SpringGossip/index.html)
[ApplicationContext 与 BeanFactory 区别 - 掘金](https://juejin.im/post/5b77d33151882542d23a0823)
[Spring Container | securenai SITE](https://securenai.github.io/spring/spring_page2/) {% asset_link web1.png 備份圖 %}

## Class

com.google.gson.annotations.Expose
GSON
[《Android》『JSON & GSON』- GSON 的基本程式語法教學 (下) - 賽肥膩膩の食旅生活誌](https://xnfood.com.tw/android-json-gson-2/)

JAVA IO
[無邊界的想像力: Java SE 入門教學 - IO 流](https://ethan-imagination.blogspot.com/2018/12/javase-gettingstarted-019.html)
[java读取文件API速度对比_Java_fengxingzhe001的专栏-CSDN博客](https://blog.csdn.net/fengxingzhe001/article/details/67640083)
[Java讀取寫出檔案FileReader和FileWriter | CYL菜鳥攻略 - 點部落](https://dotblogs.com.tw/cylcode/2018/09/21/170510)

java.io.BufferedWriter
java.io.ByteArrayOutputStream
java.io.FileInputStream
java.io.FileOutputStream
java.io.IOException
java.io.PrintWriter
java.io.Serializable
java.io.Writer
java.io.File

要曲檔名 file.getName
路徑 file.getPath 會包含檔名

要怎麼曲資料夾路徑
[java - How do I get a file's directory using the File object? - Stack Overflow](https://stackoverflow.com/questions/3657157/how-do-i-get-a-files-directory-using-the-file-object)



File.getParent()  可能會抓到 null
```java
File file = new File("test.txt");
String parent = file.getParent();
```
所以推薦

```java
parent = file.getAbsoluteFile().getParent();
```

MATH
java.math.BigDecimal

ip
java.net.InetAddress

[InetAddress (Java Platform SE 7 )](https://docs.oracle.com/javase/7/docs/api/java/net/InetAddress.html)
[InetAddress 類別](https://openhome.cc/Gossip/JavaGossip-V2/InetAddress.htm)
[利用Java獲得IP地址 @ Java 咖啡 :: 隨意窩 Xuite日誌](https://blog.xuite.net/x_3kkk/java/13809146-%E5%88%A9%E7%94%A8Java%E7%8D%B2%E5%BE%97IP%E5%9C%B0%E5%9D%80)

處理時間格式
java.text.SimpleDateFormat
[Java：日期時間格式化輸出入處理：Date、Calendar、SimpleDateFormat @ 符碼記憶](https://www.ewdna.com/2009/01/javadatecalendardateformatsimpledatefor.html)


java.util
Contains the collections framework, legacy collection classes, event model, date and time facilities, internationalization, and miscellaneous utility classes (a string tokenizer, a random-number generator, and a bit array).

java.util.ArrayList
java.util.Calendar
java.util.Collections
java.util.Date
java.util.HashMap
java.util.HashSet
java.util.LinkedHashMap
java.util.List
java.util.Map
java.util.Set
java.util.UUID


javax.crypto.IllegalBlockSizeException
[JAVA实现AES加密-云栖社区-阿里云](https://yq.aliyun.com/articles/533581)

javax.persistence
Java Persistence is the API for the management for persistence and object/relational mapping.

javax.persistence.Column
javax.persistence.Entity
javax.persistence.FetchType
javax.persistence.GeneratedValue
javax.persistence.GenerationType
javax.persistence.Id
javax.persistence.JoinColumn
javax.persistence.ManyToOne
javax.persistence.SequenceGenerator
javax.persistence.Table
javax.persistence.Temporal
javax.persistence.TemporalType


javax.servlet.http.HttpServletRequest
javax.servlet.http.HttpServletResponse


net.sf.json.JSONObject
[JSON用法—net.sf.json.JSONObject_Java_午夜阳光-CSDN博客](https://blog.csdn.net/u013514928/article/details/79114814)

org.apache.commons.io.FileUtils
[org.apache.commons.io.FileUtils 详解_Java_蚩尤后裔-CSDN博客](https://blog.csdn.net/wangmx1993328/article/details/80136921)

org.apache.commons.lang.builder.ToStringBuilder
org.apache.commons.lang.builder.ToStringStyle
org.apache.commons.lang.StringUtils
[Commons Lang使用ToStringBuilder - 就是愛Java](http://java.openyu.org/2014/03/commons-lang3-tostringbuilder_18.html)


[Javalogy–Java的學習筆記: Commons Logging](http://java-logy.blogspot.com/2007/11/commons-logging.html)
org.apache.commons.logging.Log
org.apache.commons.logging.LogFactory
org.apache.log4j.Logger

[Apache POI超鏈接 - POI基礎教程](http://tw.gitbook.net/apache_poi/apache_poi_hyperlink.html)
org.apache.poi.ss.usermodel.Row
org.apache.poi.xssf.usermodel.XSSFRow
org.apache.poi.xssf.usermodel.XSSFSheet
org.apache.poi.xssf.usermodel.XSSFWorkbook


org.hibernate.Query
[第二個 Hibernate](https://openhome.cc/Gossip/HibernateGossip/SecondHibernate.html)


org.junit.runner.RunWith
org.junit.Test
[JUnit 5和JUnit 4比较_Java_javaZhong的博客-CSDN博客](https://blog.csdn.net/u010675669/article/details/86574956)
[使用 JUnit 4.x](https://openhome.cc/Gossip/JUnit/JUnit4.html)

org.springframework.beans.BeanUtils
[Spring 中的BeanUtils与apache中的BeanUtils差别_Java_langqiao123的专栏-CSDN博客](https://blog.csdn.net/langqiao123/article/details/72961332)

org.springframework.beans.factory.annotation.Autowired
[IoC - Annotation (@Autowired) - 史帝芬心得筆記](https://sites.google.com/site/stevenattw/java/spring-framework/ioc---annotation-autowired)

org.springframework.beans.factory.annotation.Qualifier
[详解@Autowired、@Qualifier和@Required_Java_Benjamin-CSDN博客](https://blog.csdn.net/benjamin_whx/article/details/24659745)

org.springframework.context.support.AbstractApplicationContext
org.springframework.context.support.ClassPathXmlApplicationContext
[Spring 入门学习二之IOC - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000015047555)

org.springframework.stereotype.Repository


org.springframework.stereotype.Service
[SSM简单整合教程&测试事务 - 东北小狐狸 - 博客园](https://www.cnblogs.com/hellxz/p/7282454.html)
[Day 8 Spring MVC-@Service的使用 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10194079)

org.springframework.test.context.ContextConfiguration
[Sprin g整合 JUnit4 测试时，使用 @ContextConfiguration 注解引入多个配置文件_Java_HeatDeath的博客-CSDN博客](https://blog.csdn.net/HeatDeath/article/details/79364562)


org.springframework.test.context.junit4.SpringJUnit4ClassRunner
org.springframework.transaction.annotation.Propagation
[Spring中@Transactional用法詳細介紹 | 程式前沿](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/312313/)

org.springframework.transaction.annotation.Transactional
[框架源码系列十一：事务管理（Spring事务管理的特点、事务概念学习、Spring事务使用学习、Spring事务管理API学习、Spring事务源码学习） - 小不点啊 - 博客园](https://www.cnblogs.com/leeSmall/p/10306672.html)

org.springframework.web.bind.ServletRequestUtils
[Spring中好用的工具类 - 作业部落 Cmd Markdown 编辑阅读器](https://www.zybuluo.com/Cesar/note/393815)


Calendar 
[為什麼不建議使用Date，而是使用Java8新的時間和日期API？ - IT閱讀](https://www.itread01.com/content/1568272383.html)



## 其他為整理

[Struts2 和 springMVC 的區別 - IT閱讀](https://www.itread01.com/content/1544703608.html)
[Struts2 Spring Hibernate的工作流程及原理(整理的) | 程式前沿](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/435210/)
[Struts2下載文件實例 - Struts2基礎教程](http://tw.gitbook.net/struts_2/struts-2-download-file-example.html)
[Java : How to generate serialVersionUID – Mkyong.com](https://mkyong.com/java/how-to-generate-serialversionuid/)
[关于tomcat WEB-INF/lib下类加载顺序 - 四少啊 - 博客园](https://www.cnblogs.com/yaojf/p/9852802.html)
[struts2對json的支持 - IT閱讀](https://www.itread01.com/content/1505151616.html)
[SpringApplication你不知道的那些事！ - 掘金](https://juejin.im/post/5bc6a1c6f265da0acf0b102e)
[菜鳥工程師 肉豬: Spring @Configuration作用](https://matthung0807.blogspot.com/2019/04/spring-configuration_28.html)
[IoC - Annotation (@Configuration) - 史帝芬心得筆記](https://sites.google.com/site/stevenattw/java/spring-framework/ioc---annotation-configuration)
[菜鳥工程師 肉豬: Spring Boot properties file value injection 屬性配置檔注入](https://matthung0807.blogspot.com/2019/08/spring-boot-properties-file-value.html)
```java
@SpringBootApplication
@PropertySource(
    value = { "classpath:my-messages.properties", "classpath:other-messages.properties" }, 
    ignoreResourceNotFound = true,
    encoding = "UTF-8"
)
public class DemoApplication {
    public static void main(String[] args) {
        ApplicationContext appCtx = SpringApplication.run(DemoApplication.class, args);
        
        MyMessages myMessages = appCtx.getBean(MyMessages.class);
        OtherMessages otherMessages = appCtx.getBean(OtherMessages.class);
        
        myMessages.printPropertiesValue();
        
        System.out.println("-----------------------");
        
        otherMessages.printPropertiesValue();
    }

}
```