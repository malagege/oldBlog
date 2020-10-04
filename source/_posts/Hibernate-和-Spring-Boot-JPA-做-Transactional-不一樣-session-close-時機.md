---
title: Hibernate 和 Spring Boot JPA 做 @Transactional 不一樣 session.close 時機
date: 2020-09-06 15:01:58
tags: [hibernate, transactional, jpa, spring]
categories: Java
---

之前我有做過[Spring 交易事務失效 | 程式狂想筆記](https://malagege.github.io/blog/2020/08/27/Hibernate-%E4%BA%A4%E6%98%93%E4%BA%8B%E5%8B%99%E5%A4%B1%E6%95%88/)
我以為大概了解 Spring 交易事務原理
但是我又遇到奇怪問題
在研究了一番，終於找到一些問題

<!--more-->

## 發現問題

之前以為 Hibernate 的 save 跟 jpa save的事情差不多
由於公司專案是使用 SSH 框架
跟我練習使用 Spring boot 結果不太一樣
花了一些時間研究並觀察

首先是 Hibernate 持久層
更改資料不做save 也會被儲存

**但今天我今天進入第一個@transactional 取出來的 Entity 取進去 第二個 @transactional 做 dao.save 竟然出現錯誤
後來改成 update 就不會有這個問題**

> 之前學 Spring boot 有聽說 save 會做判斷是否 新增/修改
> 我在 Hibernate 確實都可以用
> 但這個範例竟然錯了

我後來跑去 Spring Boot JPA 發現 dao 沒有 update
這時候我懷疑 Hibernate 跟 JPA 不一樣
[spring - Update or SaveorUpdate in CRUDRespository, Is there any options available - Stack Overflow](https://stackoverflow.com/questions/24420572/update-or-saveorupdate-in-crudrespository-is-there-any-options-available)


### 為什麼會發生這個問題呢?我做 update 就可以正常修改

> Persistent狀態的物件對應於資料庫中的一筆資料，物件的id值與資料的主鍵值相同，並且Session實例尚未失效，在這期間您對物件的任何狀 態變動，在Session實例關閉（close）或Transaction實例執行commit()之後，資料庫中對應的資料也會跟著更新。
>
> 如果您將Session實例關閉（close），則Persistent狀態的物件會成為Detached狀態。
> ...
> ...
> Detached狀態的物件可以使用update()方法使之與資料庫中的對應資料再度發生關聯，此時Detached狀態的物件會變為 Persistent狀態。
> 參考: [實體物件生命週期](https://openhome.cc/Gossip/HibernateGossip/EntityObjectLifeCycle.html)

### 我的Hibernate 物件變成了Detached ??? 


我的Hibernate 物件變成了Detached ??? 
最後爬到這篇文章

> getCurrentSession得到的session是和事務綁定的session，所以：
> 1，要用getCurrentSession生產的session，就必須有事務環境，意思就是你必須在調用session方法之前，session.beginTransaction（）；就算你只是get或者query
> 2，在事務提交之後，即session.getTransaction().commit()之後，session自動關閉，所以你用getCurrentSession，只需要commit事務，不要去調用session.close()。
> 
> 3，你用的是ssh，spring為hibernate的current_session_context_class配置了一個SpringSessionContext來幫你管理getCurrentSession中的session，所以，你在OpenSessionInview的時候，spring就自動的幫你打開了session——>你在執行用AOP包裝的事務的時候，事務就開啟了——>執行你的業務方法——>提交事務（注意，hibernate管理的getCurrentSession在提交事務的時候才會關閉ession，而spring提供的這個SpringSessionContext不會） ——>opensessioninview關閉session。
> 從上面的執行流程可以看出，你在SSH集成的時候，如果用的是getCurrentSession的集成方式，就不能設置hibernate的current_session_context_class為thread，而應該空著，讓spring幫你。
> 參考:[hibernate中创建session的两种方式方式，区别在哪里？_qq_25843323的博客-CSDN博客](https://blog.csdn.net/qq_25843323/article/details/50286643) {% asset_link web2.png 備份圖 %}

Hibernate daoImpl

```java
    protected Session getSession() {  
        return this.sessionFactory.getCurrentSession();  
    }  

    public void save(T entity) throws Exception {  
        this.getSession().save(entity);  
    }  
```

Spring Boot CRUDRepository
```java
    @Transactional
    public <S extends T> S save(S entity) {
        if(this.entityInformation.isNew(entity)) {
            this.em.persist(entity);
            return entity;
        } else {
            return this.em.merge(entity);
        }
    }
```

我的SSH 專案使用this.sessionFactory.getCurrentSession(); 
daoImpl 裡面確實沒看到 session.close
上面這篇應該不是錯的
我也曾經改過 thread 也不能跑


### Hibernate @Transactional 每做一次都會 commit 並 session 關閉

上面說明總結，因為 session 使用 `getCurrentSession();`
@Transactional 會依照 session 規則，最後會做 commit
Hibernate 連線會自動關閉(session.close)
**注意這邊我講的是我公司專案，不是 Spring Boot JPA**

所以之前我不了解規則，有時候會出現 No session 問題
但是 Spring Boot JPA 就沒有這個問題
現在猜測我之前是遇到這個問題

以下可能是錯誤資訊，發現錯誤會修正 ============

### Spring Boot JPA save 使用 @Transactional 不會做 commit?

下面刪掉的跳過，應該是 Spring 傳播事務不會 commit
重複 REQUIRED 兩層不會 commit，原因是兩層都是使用同一個交易
之前 @Transactional 失效問題可能是這裡面提到問題[Spring 交易事務失效 | 程式狂想筆記](https://malagege.github.io/blog/2020/08/27/Hibernate-%E4%BA%A4%E6%98%93%E4%BA%8B%E5%8B%99%E5%A4%B1%E6%95%88/)
之前我也不是很熟....

簡單來刷，@Transactional 下面那一層不會 commit，但是他有做 flush 動作
@Transactional 回到上一層結束時候，才會做 commit
但 Spring Boot JPA commit時候，並不會像 Session 一樣會 close 掉
這一點真的很方便

Hibernate 連線問題不確定[Hibernate Tips: How to access Hibernate APIs from JPA](https://thorben-janssen.com/hibernate-tips-access-hibernate-apis-jpa/)
能不能解決

~~看這邊問題，我想到 Spring 交易事務是使用AOP代理模式去做beginTransactional，最後會做`commit`~~
~~這邊我有整理[Spring 交易事務失效 | 程式狂想筆記](https://malagege.github.io/blog/2020/08/27/Hibernate-%E4%BA%A4%E6%98%93%E4%BA%8B%E5%8B%99%E5%A4%B1%E6%95%88/)~~

~~但Spring Boot JPA 的save 怎麼沒有 commit? 能順外面用 @Transactional 做 commit 和 rollback??~~

```java
    @Transactional
    public <S extends T> S save(S entity) {
        if(this.entityInformation.isNew(entity)) {
            this.em.persist(entity);
            return entity;
        } else {
            return this.em.merge(entity);
        }
    }
```

> 主要就是判斷這個對象數據庫裡面是否存在，如果存在則更新， 如果是不存在則新增；根據事務的傳播性，在執行persist或者merge操作之後並不會直接提交事務。而是在所有循環保存之後@Transaction註解會統一flush,commit事務 ,下面介紹關於EntityManager常用API

[springboot jpa 批量保存數據--EntityManager和 JpaRepository - WenQ001 - 博客園](https://www.cnblogs.com/wenq001/p/9264861.html)

~~這邊有提到使用**persist 或者 merge 操作之後並不會直接提交事務**~~

這邊我會錯意，其實我寫這一段覺得還是哪裡怪怪的
後來查了一下
應該是 Spring 事務傳播
- [Spring事務傳播行為詳解 - 個人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000013341344)  {% asset_link web6.png 備份圖 %}
這篇寫得很棒!!
- [Spring声明式事务管理之二：核心接口API - 簡書](https://www.jianshu.com/p/1533f0f5241a)
- [java - Does Hibernate's Session.close() automatically rollback uncommitted transactions? - Stack Overflow](https://stackoverflow.com/questions/19396563/does-hibernates-session-close-automatically-rollback-uncommitted-transactions)
- [HibernateTransactionManager和DataSourceTransactionManager_qq_23476319的博客-CSDN博客](https://blog.csdn.net/qq_23476319/article/details/56043282)

### Hibernate 的 commit, flush

- [Persistence Context](https://openhome.cc/Gossip/HibernateGossip/PersistenceContext.html)
- [Hibernate —— flush和commit的区别 - Java天堂](https://www.javatt.com/p/97654)


### 什麼是 persist , merge?

這邊沒研究很清楚，`Spring Boot JPA save 使用 @Transactional 不會做 commit?`
好像就是這個問題，不會影響到

有看到使用就一定要做事務，不然沒有效果

這邊整理相關連結
1. [springboot jpa 批量保存數據--EntityManager和 JpaRepository - WenQ001 - 博客園](https://www.cnblogs.com/wenq001/p/9264861.html)
2. [java - What are the differences between the different saving methods in Hibernate? - Stack Overflow](https://stackoverflow.com/questions/161224/what-are-the-differences-between-the-different-saving-methods-in-hibernate)
3. [Hibernate save()与persist()区别_攻城狮Luke（刘健彬）-CSDN博客](https://blog.csdn.net/u010739551/article/details/47253881)
4. [hibernate的各种保存方式的区别 (save,persist,update,saveOrUpdte,merge,flush,lock)等 - DANCE WITH JAVA - BlogJava](http://www.blogjava.net/dreamstone/archive/2007/07/29/133071.html) {% asset_link web4.png 備份圖 %}
5. [Hibernate各种保存方式的区别 - 不發光的螢火蟲](https://sites.google.com/site/hzg139/home/orm-jihibernate/hibernate/hibernate) {% asset_link web3.png 備份圖 %}
6. [Hibernate save, saveOrUpdate, persist, merge, update 区别 - xiaoheike - 博客园](https://www.cnblogs.com/xiaoheike/p/5374613.html) {% asset_link web1.png 備份圖 %}
[xiaoheike/HibernateSavePersistUpdateMergeDiff](https://github.com/xiaoheike/HibernateSavePersistUpdateMergeDiff)


## try catch 到底能不能抓到 @Transactional public method ?

先說一下問題，我因為上面敘述，我接觸專案 DaoImpl.java 是使用 Hibernate  
我接觸到的專案是不會抓到 try catch <---這邊指的是 dao.save 會抓到
非使用 Spring Boot JPA 的 try catch 抓地到
所以我這邊操作出來竟然測出兩種不一樣解果

Spring Boot JPA 使用 try catch(內層)抓地到 Exception <---這邊指的是 dao.save 丟的 Exception 不是一般 Exception

那為什麼會不一樣呢?

這邊我目前猜測是 @Transactional 的關係
Spring Boot JPA 裡面 .save 就有包一層 @Transactional 所以結束會 flush
但是我目前接觸到的 DaoImpl.java 就沒有這一層
所以這是目前最有可能的

### try catch 觀察

只要進入到 @Transactional
就可以抓到這個
2020-09-08 21:42:01.333 TRACE 27527 --- [nio-8080-exec-1] o.s.t.i.TransactionInterceptor           : Completing transaction for [org.springframework.data.jpa.repository.support.SimpleJpaRepository.save] after exception: javax.persistence.PersistenceException: org.hibernate.exception.DataException: could not execute statement

Completing  中文翻譯是「完成中」

[spring-framework/TransactionAspectSupport.java at 673f83e388e5ee992340a623de5a248e95b94c34 · spring-projects/spring-framework](https://github.com/spring-projects/spring-framework/blob/673f83e388e5ee992340a623de5a248e95b94c34/spring-tx/src/main/java/org/springframework/transaction/interceptor/TransactionAspectSupport.java)

當前事務發生錯誤
2020-09-08 21:42:01.362 DEBUG 27527 --- [nio-8080-exec-1] o.s.orm.jpa.JpaTransactionManager        : Initiating transaction rollback

操作 commit 會看到 Initiating transaction commit

其實只要進入到 @Transactional 都會判斷當下使用哪一種事務方法
會依據程式碼使用和當下是否有交易連線去做

相關完整可能要看程式碼...，我找不到是哪一段 flush >.<

- [spring-framework/TransactionAspectSupport.java at 673f83e388e5ee992340a623de5a248e95b94c34 · spring-projects/spring-framework](https://github.com/spring-projects/spring-framework/blob/673f83e388e5ee992340a623de5a248e95b94c34/spring-tx/src/main/java/org/springframework/transaction/interceptor/TransactionAspectSupport.java)
- [Spring源码解析_哔哩哔哩 (゜-゜)つロ 干杯~-bilibili](https://www.bilibili.com/video/BV1oW41167AV?p=38)
- [Spring源码分析（八）深入了解事务管理的流程](https://www.lagou.com/lgeduarticle/64035.html)
- [Springboot源码分析之事务拦截和管理 | Spring For All](http://www.spring4all.com/article/15684)
- [17. Transaction Management](https://docs.spring.io/spring/docs/4.3.14.RELEASE/spring-framework-reference/html/transaction.html)

### try catch 最好寫在哪邊?

**外面一層**，之前有看到 p3c看到[alibaba/p3c: Alibaba Java Coding Guidelines pmd implements and IDE plugin](https://github.com/alibaba/p3c)

雖然看到網路上很多文章，都說 try catch(內層)裡面的catch 需要再 throw Exception
不過我測試 DAO 丟的 Exception 不管你有沒有 throw Exception
他還會丟一次，當然我猜非一般Dao 丟的 Exception 
@Transactional應該不會在幫你丟，所以要自己丟


## 總結/心得

簡單來說，Spring 交易事務不管你用什麼 DB 套件，他們使用原理都是一樣的
我這邊不一樣的結果是 Spring boot 跟 我使用 Hibernate Dao 實作導致ˋ有不一樣結果
還有 Hibernate getCurrentSession 做 commit/rollback 實作會關閉交易
但透過 @Transactional public method 就可以正常抓到 連線，沒有的話可能要注意 commit/rollback 會遇到 no session 問題
當然 Spring Boot JPA應該不用煩惱
1. 不會有 commit/rollback 自動關掉連線
2. save 使用 try catch 都能抓到問題

當然除了這些問題，[Spring 交易事務失效 | 程式狂想筆記](https://malagege.github.io/blog/2020/08/27/Hibernate-%E4%BA%A4%E6%98%93%E4%BA%8B%E5%8B%99%E5%A4%B1%E6%95%88/)
裡面也要注意 @Transactional 是否有抓到 Spring AOP特性
往往有時候都是這些問題

所以了解Spring 事務，在程式撰寫應該比較不會遇到一些問題
希望不會我不要在卡關
orz

