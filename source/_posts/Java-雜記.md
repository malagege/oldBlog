---
title: Java 雜記
date: 2020-03-28 20:24:13
tags: [java, spring ,struts2]
categories: Java
---

TODO: 尚未整理完成

<!--more-->

[The 2020 Java Developer RoadMap](https://javarevisited.blogspot.com/2019/10/the-java-developer-roadmap.html)

[Top 20 Libraries and APIs Java Developer should know](https://javarevisited.blogspot.com/2018/01/top-20-libraries-and-apis-for-java-programmers.html)

## Exception 
[Java筆記 — Exception 與 Error - Carl - Medium](https://medium.com/@clu1022/java%E7%AD%86%E8%A8%98-exception-%E8%88%87-error-dbdf9a9b0909)

## io close
[Java：浅谈InputStream的close方法 - 白羊沈歌 - 博客园](https://www.cnblogs.com/shenpengyan/p/6117814.html)
[Java OutputStream.close()关闭并释放输出流资源](http://www.51gjie.com/java/701.html)

[Snailclimb/JavaGuide: 【Java学习+面试指南】 一份涵盖大部分Java程序员所需要掌握的核心知识。](https://github.com/Snailclimb/JavaGuide#mybatis)

### Memory-mapped File
[Java中Stream和Memory-mapped File的I/O性能对比 - Xavier's Blog](http://quxiao.github.io/blog/2011/03/31/java-memory-mapped-file-stream-and-the-i-o-performance-comparison/)

### stack /heap 記憶體相關

[(1) Java技術與認證交流平台](https://www.facebook.com/groups/748837991920629/permalink/1658181240986295/)

## Java Web

### Session
- [JSP Session | 菜鸟教程](https://www.runoob.com/jsp/jsp-session.html)
每一個 war 檔 session 都是分開的，可用上面 JSP 測試

- [L316476844/distributed-session: 集群/分布式环境Session的几种策略 实现步骤和方式 1、粘性session 2、应用服务器间的session复制共享 3、基于cache DB缓存的session共享](https://github.com/L316476844/distributed-session)

- [跨war包session同步_数据库_阳光下的立人-CSDN博客](https://blog.csdn.net/sun305355024sun/article/details/6928370)

## spring core

[介绍 | 走心的Java Web入门开发教程](https://www.ctolib.com/docs/sfile/Heart-First-JavaWeb/index.html)
[開源框架: Spring Gossip](https://openhome.cc/Gossip/SpringGossip/index.html)
[ApplicationContext 与 BeanFactory 区别 - 掘金](https://juejin.im/post/5b77d33151882542d23a0823)
[Spring Container | securenai SITE](https://securenai.github.io/spring/spring_page2/) {% asset_link web1.png 備份圖 %}

[Spring第一篇【介绍Spring、引入Spring、Spring六大模块】 - 掘金](https://juejin.im/post/5a6476cdf265da3e243b9e61)

[什么是面向切面编程AOP？ - 知乎](https://www.zhihu.com/question/24863332)

[Spring|@Autowired与new的区别 - 无聊的三文鸡 - 博客园](https://www.cnblogs.com/maikucha/p/10145169.html)

autowired 動態

[java - how to use '@Qualifier' dynamically specifying parameters? - Stack Overflow](https://stackoverflow.com/questions/54193516/how-to-use-qualifier-dynamically-specifying-parameters)
[spring - 如何使用运行时“限定符”变量动态注入服务？ - IT工具网](https://www.coder.work/article/56406)

autowired 常見錯誤
[Autowired · Brian的雜記](https://wujincheng.gitbooks.io/brian/java/spring/autowired.html)

[@Autowired和static的关系 - 左手程序，右手诗 - 博客园](https://www.cnblogs.com/pangyangqi/p/10010866.html)
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

Path

[Java NIO之擁抱Path和Files | 程式前沿](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/282/)

```java
File file = new File("C:/my.ini");
Path p1 = file.toPath();
p1.toFile();
file.toURI();
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
[Apache Commons工具集简介 - 坚持学习 - ITeye博客](https://www.iteye.com/blog/zhoualine-1770014)

org.apache.commons.lang.builder.ToStringBuilder
org.apache.commons.lang.builder.ToStringStyle
org.apache.commons.lang.StringUtils
[Commons Lang使用ToStringBuilder - 就是愛Java](http://java.openyu.org/2014/03/commons-lang3-tostringbuilder_18.html)
[[Java基础]StringUtils.join()方法与String.join()方法的使用_Java_YuanMxy'Home-CSDN博客](https://blog.csdn.net/YuanMxy/article/details/81012697)

org.apache.commons.io.IOUtils
[java - Should I close the InputStream of org.apache.commons.io.IOUtils - Stack Overflow](https://stackoverflow.com/questions/36861650/should-i-close-the-inputstream-of-org-apache-commons-io-ioutils)

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
[透彻的掌握 Spring 中@transactional 的使用](https://www.ibm.com/developerworks/cn/java/j-master-spring-transactional-use/index.html)

org.springframework.web.bind.ServletRequestUtils
[Spring中好用的工具类 - 作业部落 Cmd Markdown 编辑阅读器](https://www.zybuluo.com/Cesar/note/393815)


Calendar 
[為什麼不建議使用Date，而是使用Java8新的時間和日期API？ - IT閱讀](https://www.itread01.com/content/1568272383.html)





## Struts

### 詳細教學

[Struts2第二篇【开发步骤、执行流程、struts - 掘金](https://juejin.im/post/5a64772a6fb9a01cb31647e3)
[Struts2第三篇【Action开发方式、通配符、Struts常量、跳转全局视图、action节点默认配置】 - 掘金](https://juejin.im/post/5a6476cc51882573392cd64b)
[Spring第四篇【Intellij idea环境下、Struts2和Spring整合】 - 掘金](https://juejin.im/post/5a6476ed6fb9a01cbc6e7e44)
[Struts2第五篇【类型转换器、全局、局部类型转换器】 - 掘金](https://juejin.im/post/5a64770ef265da3e54692b05)
[Struts2第六篇【文件上传和下载】 - 掘金](https://juejin.im/post/5a6476e36fb9a01c975a58cf)
[Struts2第七篇【介绍拦截器、自定义拦截器、执行流程、应用】 - 掘金](https://juejin.im/post/5a647729518825732d7fb34b)
[Struts第八篇【资源国际化、对比JSP的资源国际化】 - 掘金](https://juejin.im/post/5a6476a6518825732b19f002)
[Struts2第九篇【OGNL、valueStack详解】 - 掘金](https://juejin.im/post/5a64773d6fb9a01ca071ef96)
[Struts2第十一篇【简单UI标签、数据回显】 - 掘金](https://juejin.im/post/5a6476f3f265da3e33046e47)
[Struts2第十二篇【模型驱动】 - 掘金](https://juejin.im/post/5a6476cd518825732d7fb347)
[Struts2第十三篇【防止表单重复提交拦截器】 - 掘金](https://juejin.im/post/5a6476ad51882573485a0d86)



[Struts2入门这一篇就够了 - 掘金](https://juejin.im/post/5aa3349ff265da23884caa6a)

[Struts2的配置 struts.xml Action詳解 | 程式前沿](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/303709/)

### 數據封裝

struts2 最近使用發現他在表單傳送 input 值真的很方便
只要對 Java 做 getXxxx , setXxxx (動態) 就能做很多設定
後來查到這名詞是數據封裝
有很多種方式很已做到
詳細如:[struts2(二) 表单参数自动封装和参数类型自动转换 - 漂泊在外的程序员老王 - 博客园](https://www.cnblogs.com/whgk/p/6587130.html)


[struts2转换器详解_Java_xtayfjpk的专栏-CSDN博客](https://blog.csdn.net/xtayfjpk/article/details/16826649)

### struts.xml

- [Struts2配置文件【代码库】 - 掘金](https://juejin.im/post/5a6476b1f265da3e55380273)
- [7、struts.xml配置详解 - 暗夜亡灵 - 博客园](https://www.cnblogs.com/fmricky/archive/2010/05/20/1740479.html)
- [Struts 2 result详解 - 簡書](https://www.jianshu.com/p/d8ba3a768447)
- [java - How to return HTTP error status code *and* content in Struts2 - Stack Overflow](https://stackoverflow.com/questions/5904821/how-to-return-http-error-status-code-and-content-in-struts2)(未測試)
- [菜鸟学Struts2——Results - 92年程序员 - 博客园](https://www.cnblogs.com/niloay/p/struts2-result.html)
- 

#### 最佳程式路徑

曾經第一次接觸使用 strut2
我常用 XML 設定如下

```xml
<package name="vvvv" namespace="/vvvv" extends="struts-default">
    <action name="insertStock" method="doInsertStock" class="vvvv.action.Stock">
        <result name="success">/vvvv/pages/stock_update.jsp</result>
        <result name="error">/vvvv/pages/stock_insert.jsp</result>
    </action>
    <action name="addStock" method="doAddStock" class="vvvv.action.Stock">
        <result name="success">/vvvv/pages/stock_insert.jsp</result>
    </action>
...
```

通常一個 class 跟 method 對應一個設定
但我發現 xml 的 action method 設定不一定要放(預設是 execute)
每一個 method 不一定需要設定 不一定要放多個 tag 如範例
只要設定一個 action 就能做到了
可參考下面設定

```xml
<action name="oooo" class="com.oooo.action.XxxxAction">
    <result name="success">/ooooo/oooo/success.jsp</result>
    <result name="list">/ooooo/oooo/query.jsp</result>
    <result name="edit">/ooooo/oooo/edit.jsp</result>
</action> 
```

其他，看到優良設定
```xml
<package name="user" namespace="/" extends="struts-default"> 
<action name="*user" class="com.zzjmay.action.VaildateAction" method="{1}"> 
<result name="success">/success.jsp</result> 
<result name="input">/{1}.jsp</result> 
</action> 
</package> 
```
> 1. name:邏輯上的包名
> 2. namespace:名稱空間，用來區分同意struts下的相同action（注意是一種邏輯上的的路徑）
> 3. extends: 繼承某個配置檔案
> 4. abstract
> 1. name:Action類的對映名稱
> 2. class:Action類的完整路徑
> 3. method:預設使用的execute()方法，如果要自定義觸發方法，需要使用method自定義

[Struts2的配置 struts.xml Action詳解 | 程式前沿](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/303709/)

還有更彈性設定

```xml
    <action name="user_*_*" method="user{2}" class="com.gopain.{1}">
    <result name="type_{2}">/{2}_page.jsp</result>
    </action>
```
> 假如請求為，user_UserAction_login（不考慮namespace），其中 action中{1} 為UserAction，{2}為login，此請求會執行com.gopain.UserAction$userlogin().如果返回值為」type_login「,則會跳轉到login_page.jsp頁面。
> 
> 此處只是舉例，站點不宜這樣配置請求。但是對於action的name配置很實用，不要奢望一個action配置完整個站點（雖然也可以實現，而且對於訪問沒有太多影響），十分不利於管理。
> ————————————————
> 版权声明：本文为CSDN博主「gopain」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
> 原文链接：https://blog.csdn.net/gopain/java/article/details/40790523

參考:[[Struts2] 配置文件struts.xml和web.xml詳解_網絡_gopain-CSDN博客](https://blog.csdn.net/gopain/article/details/40790523)

管理上還請斟酌，不過用第二個方法就很方便了

### 與 spring 設定

[struts2默认拦截器之autowiring_Java_shendeguang的专栏-CSDN博客](https://blog.csdn.net/shendeguang/article/details/8194871)

### action 到 jsp 傳值

[struts2中action传值到jsp页面的3种方法 - 开源中国首席吊炸天的个人空间 - OSCHINA](https://my.oschina.net/u/3058325/blog/804000)

## struts2 錯誤處理
[起風前的相遇: Struts2 處理404錯誤的方法](http://mactruecolor.blogspot.com/2011/07/struts2-404.html)

### 攔截器

- [Struts2【拦截器】就是这么简单 - 知乎](https://zhuanlan.zhihu.com/p/34433146)
`<interceptor-stack name="defaultStack">` 宣告攔截器...

#### JSON 設定

```xml
  <package name="default" namespace="/" extends="json-default">
		<global-results>
			<result name="dataResult" type="json">
				<!-- <param name="root">jsonresult_var</param> 針對 action 的 變數當 json 的 root
				<param name="contentType">text/json</param>  --> 
			</result>
            ....
```

參考:[Struts2和JSON實例 - Struts2教學](http://tw.gitbook.net/struts_2/struts-2-and-json-example.html)

更多設定可參考
[JSON plugin](https://struts.apache.org/plugins/json/)

#### 下載檔案

```xml
			<result name="download" type="stream">
				<param name="contentType">application/octet-stream</param>
				<param name="inputName">fileStream</param>
				<param name="contentDisposition">attachment;filename="${fileName}"</param>
				<param name="bufferSize">1024</param>
	  		</result>
```

> 在上述配置代碼中，分別通過 stream 結果類型的屬性設置了所要下載文件的類型、名稱和輸入流。其中 ${filename} 表示在項目運行時，將 action 中的 filename 屬性動態地填充在 ${} 中間部分，這樣就可以動態地獲取所要下載的文件名稱。

通常上述關係，會做 setFileName(xxx)

參考:[Struts2文件下载实例](http://c.biancheng.net/view/4160.html)


#### 移除 url .action

在 struts.xml 加入 tag 可以解決這個問題
```xml
<struts>
    <constant name="struts.action.extension" value=""/> 
</struts>
```
不過我發現預設是 ,,action ，代表.action 跟空白也可以使用
上述詳細教學第三章有提到

[java - Struts 2 - Removing action from url - Stack Overflow](https://stackoverflow.com/questions/17160838/struts-2-removing-action-from-url)
[如何刪除Struts2動作的後綴擴展名 - Struts2教學](http://tw.gitbook.net/struts_2/how-to-remove-the-action-suffix-extension-in-struts-2.html)


## Hibernate

[Hibernate第一篇【介绍Hibernate，简述ORM，快速入门】 - 掘金](https://juejin.im/post/5a64773ef265da3e2b168426)
[Hibernate第二篇【API讲解、执行流程图】 - 掘金](https://juejin.im/post/5a6477186fb9a01ca10b02a4)
[Hibernate第三篇【主配置文件、映射文件、复合主键映射】 - 掘金](https://juejin.im/post/5a64771ef265da3e2a0db002)
[Hibernate第五篇【inverse、cascade属性详解】 - 掘金](https://juejin.im/post/5a6476ab6fb9a01c95263107)
[Hibernate第六篇【多对多映射、一对一映射】 - 掘金](https://juejin.im/post/5a6476c5f265da3e36415c2c)
[Hibernate第七篇【对象状态、一级缓存】 - 掘金](https://juejin.im/post/5a64773a51882573336625f5)
[Hibernate第八篇【懒加载】 - 掘金](https://juejin.im/post/5a6476ea51882573256be7ae)
[Hibernate第九篇【组件映射、继承映射】 - 掘金](https://juejin.im/post/5a6477196fb9a01c96583d5a)
[Hibernate第十篇【Hibernate查询详解、分页查询】 - 掘金](https://juejin.im/post/5a647712518825733c142733)
[Hibernate第十一篇【配置C3P0数据库连接池、线程Session】 - 掘金](https://juejin.im/post/5a6476f1f265da3e5b32e632)
[Hibernate第十二篇【二级缓存介绍、缓存策略、查询缓存、集合缓存】 - 掘金](https://juejin.im/post/5a6476a9f265da3e484beb53)

### Hibernate占位符问题[use named parameters or JPA-style positional parameters instead.]
[Hibernate占位符问题[use named parameters or JPA-style positional parameters instead.]_Java_鹏霄万里展雄飞-CSDN博客](https://blog.csdn.net/wangpeng047/article/details/12042051)

### OpenSessionInViewFilter

- [[转]Spring配置之OpenSessionInViewFilter - yunlvrensheng - 博客园](https://www.cnblogs.com/ZhuRenWang/p/4708973.html)
- [ssh中org.springframework.orm.hibernate4.support.OpenSessionInViewFilter的作用及配置 - 243573295 - 博客园](https://www.cnblogs.com/hwaggLee/p/5604095.html)

## 其他為整理

[thymeleaf的下拉框(select option)回显选中_Java_皮皮鹏的博客-CSDN博客](https://blog.csdn.net/qq_34262794/article/details/80380566)
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

[Spring Boot Bean和依赖注入 - Spring Boot教程™](https://www.yiibai.com/spring-boot/spring_boot_beans_and_dependency_injection.html)
[Springboot2教程：@SpringBootApplication自动配置-Java知音](https://www.javazhiyin.com/28012.html)
[整合Spring时Service层为什么不做全局包扫描详解_慕课手记](https://www.imooc.com/article/16155)
[用最简单的语言来告诉你:SpringMVC中@Controller,@Service,@repository,@Component的用法 - HelloJava菜鸟社区](https://www.hellojava.com/a/25881.html)
[构建工具的进化：ant, maven, gradle - 知乎](https://zhuanlan.zhihu.com/p/24429133)
[给女朋友讲解什么是代理模式](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484222&idx=1&sn=5191aca33f7b331adaef11c5e07df468&chksm=ebd7423fdca0cb29cdc59b4c79afcda9a44b9206806d2212a1b807c9f5879674934c37c250a1#rd)
[解决错误The type List is not generic; it cannot be parameterized with arguments _Java_瓜爪牛的博客-CSDN博客](https://blog.csdn.net/zbdxcyg/article/details/71440729)

## Annotation

[Java注解（Annotation）原理详解_Java_心中要有一片海的专栏-CSDN博客](https://blog.csdn.net/lylwo317/article/details/52163304)
[Java:Annotation(注解)--原理到案例 - 簡書](https://www.jianshu.com/p/28edf5352b63)
[关于Java的Retention元注解 - 摆渡者 - OSCHINA](https://my.oschina.net/itblog/blog/1525665)
[Java 注解之 元注解 - 知乎](https://zhuanlan.zhihu.com/p/56713785) {% asset_link web2.png 備份圖 %}
裡面講的還滿細的!!

## JAXRS

[jersey - Difference between JAX-RS and Spring Rest - Stack Overflow](https://stackoverflow.com/questions/42944777/difference-between-jax-rs-and-spring-rest)

[Spring MVC与JAX-RS比较与分析 - InfoQ](https://www.infoq.cn/article/springmvc_jsx-rs)


spring version
[2. New Features and Enhancements in Spring 3.0](https://docs.spring.io/spring/docs/3.0.x/spring-framework-reference/html/new-in-3.html)
[[JAVA_Spring]Spring 3.2.2的各類jar說明 @ 程式開發學習之路 :: 痞客邦 ::](https://pclevinblog.pixnet.net/blog/post/314562535-spring-3.2.2%E7%9A%84%E5%90%84%E9%A1%9Ejar%E8%AA%AA%E6%98%8E)
[Blog of Bruce: Hibernate 4.x 整合 Spring 3.x](http://javabruce.blogspot.com/2016/02/hibernate4x-spring3x.html)

[Spring：3大核心版本簡介、maven與Spring4構建工程、代碼實戰 - 每日頭條](https://kknews.cc/zh-tw/code/gzvy35y.html)
[Spring4新特性——Web开发的增强 - 《亿级流量网站架构核心技术》~ - ITeye博客](https://www.iteye.com/blog/jinnianshilongnian-1989381)
[Spring 5.x 新特性 - 掘金](https://juejin.im/post/5c6509d9f265da2dd37c0397)

## SLF4J

[SLF4J使用和与Log4J对比 - 簡書](https://www.jianshu.com/p/32e2a7254c03)

## java class list
[reflection - Java - Get a list of all Classes loaded in the JVM - Stack Overflow](https://stackoverflow.com/questions/2548384/java-get-a-list-of-all-classes-loaded-in-the-jvm)

JAVA_OPTS="$JAVA_OPTS -verbose:class"

## ejb

[語言技術: EJB3 Gossip](https://openhome.cc/Gossip/EJB3Gossip/index.html)
[[轉] EJB 3和Spring技術體系比較 - yjmyzz @ IT工程師數位筆記本 :: 痞客邦 ::](http://newgoodlooking.pixnet.net/blog/post/125466267)
 [簡單鋪設Enterprise Java Bean(EJB)的架構 @ 資訊迴圈 :: 隨意窩 Xuite日誌](https://blog.xuite.net/jialiang/programs/39229990-%E7%B0%A1%E5%96%AE%E9%8B%AA%E8%A8%ADEnterprise+Java+Bean%28EJB%29%E7%9A%84%E6%9E%B6%E6%A7%8B)

 ```
java ee基本是在企業裡用的，你編程以前，要配置一大堆xml這樣子，很可能會是javaee
java se基本都用在學校，教學，科學實驗這些，配置個maven就了不起了
java me就是沒人用的那些...
```
[java ee , java se, java me 怎麼區分? - 知乎](https://www.zhihu.com/question/31455874)
[Spring 上課筆記 (2) | Leo Yeh's Blog](https://leoyeh.me/2016/10/15/Spring-%E4%B8%8A%E8%AA%B2%E7%AD%86%E8%A8%98-2/)

## sitemesh

[SiteMesh初體驗 @ Jemmy Walker :: 痞客邦 ::](https://jemmywalker.pixnet.net/blog/post/38324103?m=on//////)
[java - SiteMesh - still active? v2 vs v3? - Stack Overflow](https://stackoverflow.com/questions/3197370/sitemesh-still-active-v2-vs-v3)
[[页面模板框架对比] Apache Tiles VS Sitemesh_Java_孤天浪雨-CSDN博客](https://blog.csdn.net/u010246789/article/details/52343526)
[Struts2 使用SiteMesh做网页布局 - Jersey - 博客园](https://www.cnblogs.com/jersey/archive/2011/04/10/2011244.html)


## JNDI

[Spring / Hibernate / JUnit - No Hibernate Session bound to Thread - Stack Overflow](https://stackoverflow.com/questions/734614/spring-hibernate-junit-no-hibernate-session-bound-to-thread)

[放棄思考: JNDI + Derby + TomEE Plus 教學](https://javatoybox.blogspot.com/2018/09/jndi-derby-tomee-plus.html)


## ReflectionToStringBuilder

[ReflectionToStringBuilder用法优缺点及一个问题和分享_Darrensty的博客-CSDN博客_reflectiontostringbuilder](https://blog.csdn.net/Darrensty/article/details/78395221)



## Pretty-Print JSON in Java

GSON

```java
Gson gson = new GsonBuilder().setPrettyPrinting().create();
JsonParser jp = new JsonParser();
JsonElement je = jp.parse(uglyJSONString);
String prettyJsonString = gson.toJson(je);
```


存 Java

```Java
JSONObject json = new JSONObject(jsonString); // Convert text to object
System.out.println(json.toString(4)); // Print it with specified indentation
```

## 抓取檔案位置

抓取編譯檔案

```java
getClass().getClassLoader().getResource("main/resources/simple/xxx.xlsx").getFile();
```

讀取程式執行路徑


1

```java
return new File(MyClass.class.getProtectionDomain().getCodeSource().getLocation()
    .toURI()).getPath();
```

[java - How to get the path of a running JAR file? - Stack Overflow](https://stackoverflow.com/questions/320542/how-to-get-the-path-of-a-running-jar-file)

## Procedure 
- [oracle - Can I rollback after calling stored procedure in java test? - Stack Overflow](https://stackoverflow.com/questions/46051642/can-i-rollback-after-calling-stored-procedure-in-java-test)
- [使用非同步處理提升資料庫更新速度-黑暗執行緒](https://blog.darkthread.net/blog/async-batch-update-performance/)
- [寫好SQL很有必要！ - 數據分析那些事 - Medium](https://medium.com/@allaboutdataanalysis/%E5%AF%AB%E5%A5%BDsql%E5%BE%88%E6%9C%89%E5%BF%85%E8%A6%81-3206014d5422)
- [阿肥的DB學習之路: 四月 2017](http://dboffat.blogspot.com/2017/04/?m=1)


RxJava2+ Retrofit2

* [RxJava2+ Retrofit2 簡介](https://tpu.thinkpower.com.tw/tpu/articleDetails/1306)


[java web中po 和dto之间的使用 - 的回答 - SegmentFault 思否](https://segmentfault.com/q/1010000018530184/a-1020000018530356?sort=created)
[项目结构介绍-博客](https://iambigboss.top/post/59663_1_1.html)
[PO BO VO DTO POJO DAO 概念及其作用（附转换图）](http://www.360doc.cn/article/15242507_435666535.html)
[数据模型 - 阿里规约（1） | Ironbin](http://onthink.com/2018/07/22/alibaba-models/)





基礎於：Java、Amazon AWS，進行：開發，測試，佈署（Code / Infra），維運。
Spring MVC Framework的Servlet Container 應用服務開發。
Vert.X 或 Micronaut 的微服務Microservices開發。
The Reactive Manifesto的Reactive Friendly程式撰寫風格（Plus)。 

 基礎於：Java、Amazon AWS，進行：開發，測試，佈署（Code / Infra），維運。
Spring MVC Framework的Servlet Container 應用服務開發。
Vert.X 或 Micronaut 的微服務Microservices開發。
Apache Kafka的事件及訊息驅動應用開發。
The Reactive Manifesto的Reactive Friendly程式撰寫風格（Plus)。
Cloudformation 腳本撰寫。
懂或有意願學習：Jenkins + CodePipeline 腳本撰寫經驗。
協助導入及實作AWS及On Premises 混合雲