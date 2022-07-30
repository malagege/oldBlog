---
title: Spring 建立多個連線方法
date: 2022-07-31 02:26:05
tags: [java,spring]
categories: Java
---

有一部分寫到一半沒寫完。

<!--more-->

## 網路上整理資料


[Spring的多事务配置（多个Transaction Manager）和使用方法 - 简书](https://www.jianshu.com/p/c5b3d8c74f81)

[Spring的多事务配置（多个Transaction Manager）和使用方法_Clement-Xu的专栏-CSDN博客](https://blog.csdn.net/ClementAD/article/details/47275227?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522163572921416780366523245%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=163572921416780366523245&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_v2~rank_v29-1-47275227.pc_v2_rank_blog_default&utm_term=transactin+manager+&spm=1018.2226.3001.4450)

[Spring3.0配置多個事務管理器的方法 - IT閱讀](https://www.itread01.com/article/1494722449.html)


[DataSource和SessionFactory的区别_Zzrdark_的博客-CSDN博客](https://blog.csdn.net/Zzrdark_/article/details/54427907)


[如何在spring框架中解决多数据源的问题 - duanxz - 博客园](https://www.cnblogs.com/duanxz/p/4516388.html)


[如何在spring框架中解决多数据源的问题_runming56的专栏-CSDN博客](https://blog.csdn.net/runming56/article/details/18727447)

> The txManager bean in this case is of the HibernateTransactionManager type. In the same way as the DataSourceTransactionManager needs a reference to the DataSource, the HibernateTransactionManager needs a reference to the SessionFactory.

[16. Transaction Management](https://docs.spring.io/spring-framework/docs/4.2.x/spring-framework-reference/html/transaction.html#transaction-strategies)

[13.3 Hibernate](https://docs.spring.io/spring-framework/docs/3.0.0.M4/reference/html/ch13s03.html)


## transactionManager 預設抓 transactionManager

```
The default <tx:annotation-driven> target bean name transactionManager will still be used if no specifically qualified PlatformTransactionManager bean is found.

在配置檔案中，預設情況下，<tx:annotation-driven>會自動使用名稱為transactionManager的事務管理器。所以，如果定義的事務管理器名稱為transactionManager，那麼就可以直接使用<tx:annotation-driven/>

```



## 我公司的專案使用上面方法無效


參照[Spring的多事务配置（多个Transaction Manager）和使用方法 - 简书](https://www.jianshu.com/p/c5b3d8c74f81)，照上面各種設定發現我的傳案無法正常使用，後來發現Dao繼承的`GenericDao`裡面的`SessionFactory`寫死，所以上面怎麼設定都會連到，想嘗試抓現在SessionFactory，看目前沒有想到方法。




### Workaround 解決方法

1. 設定 applicationContext.xml
```xml
 	<bean id="dataSource" class="org.springframework.jndi.JndiObjectFactoryBean"> 
		<property name="jndiName"> 
			<value>java:/xxxPool</value>
			<value>java:comp/env/xxxPool</value>
		</property>
		<property name="cache" value="false" />	 
	</bean>
	
	<bean id="dataSourceHistory" class="org.springframework.jndi.JndiObjectFactoryBean"> 
		<property name="jndiName"> 
			<value>java:comp/env/xxxPool</value>
		</property>
		<property name="cache" value="false" />	 
	</bean>
	<bean id="sessionFactory" class="org.springframework.orm.hibernate4.LocalSessionFactoryBean">
		<property name="dataSource" ref="dataSource"/>
		<property name="packagesToScan" value="com.Project.persistence.entity" />
        <property name="hibernateProperties">
            <props>
                <prop key="hibernate.dialect">
                    org.hibernate.dialect.PostgreSQLDialect
                </prop>
                <prop key="javax.persistence.validation.mode">none</prop>
                <prop key="hibernate.show_sql">true</prop>
                <prop key="hibernate.format_sql">true</prop>
                <!-- prop key="hibernate.default_schema">Project</prop -->
                <prop key="hibernate.current_session_context_class">org.springframework.orm.hibernate4.SpringSessionContext</prop>
            </props>
        </property>
		<property name="mappingResources">
			<list>
				<value>com/Project/conf/xxxx_sql.xml</value>
			</list>
		</property>	
	</bean>
	<bean id="historySessionFactory" class="org.springframework.orm.hibernate4.LocalSessionFactoryBean">
		<property name="dataSource" ref="dataSourceHistory"/>
		<property name="packagesToScan" value="com.Project.persistence.history.entity" />
        <property name="hibernateProperties">
            <props>
                <prop key="hibernate.dialect">
                    org.hibernate.dialect.PostgreSQLDialect
                </prop>
                <prop key="javax.persistence.validation.mode">none</prop>
                <prop key="hibernate.show_sql">true</prop>
                <prop key="hibernate.format_sql">true</prop>
                <prop key="hibernate.default_schema">history</prop>
                <prop key="hibernate.current_session_context_class">org.springframework.orm.hibernate4.SpringSessionContext</prop>
            </props>
        </property>
	</bean>
<tx:annotation-driven transaction-manager="transactionManager"/>
....

	<bean id="transactionManager" class="org.springframework.orm.hibernate4.HibernateTransactionManager">
		<property name="sessionFactory" ref="sessionFactory"/>
		<qualifier value="ProjectTx"/>
	</bean>

	<bean id="historyTransactionManager" class="org.springframework.orm.hibernate4.HibernateTransactionManager">
		<property name="sessionFactory" ref="historySessionFactory"/>
		<qualifier value="historyTx"/>
```

2. 針對原本 Bean 建立物件，去做 ForHistory名稱，原因是 packagesToScan 時候，不能用相同 Entry 名稱，所以要特別命名。

XXXX.java ==> XXXXForHistory.java

3. 複製 `GenericDaoImpl` 維 `GenericDaoForHistoryImpl`

因為`GenericDaoImpl` 裡面 SessionFactory 寫死，所以為什麼上面設定怎麼調整，所以沒法修改到連線，這邊也是我目前沒辦法動態載入到連線(SessionFactory)地方。

```java=
public class GenericDaoImpl<T> implements GenericDao<T> { 
	
	private Class<T> clazz;  
	
    public GenericDaoImpl() {  
        ParameterizedType type = (ParameterizedType) this.getClass().getGenericSuperclass();  
        clazz = (Class<T>) type.getActualTypeArguments()[0];
    }  

    @Resource  
    private SessionFactory sessionFactory;  
```


改為 
```java=
	@Resource  
	private SessionFactory historySessionFactory;   
```

![](https://i.imgur.com/qUkJKEr.png)

`@Resource`可以參考 [Spring 自動注入小記 | 程式狂想筆記](https://malagege.github.io/blog/2020/05/11/Spring-%E8%87%AA%E5%8B%95%E6%B3%A8%E5%85%A5%E5%B0%8F%E8%A8%98/)

4. 查詢SQL設定`@Transactional`另一條連線

我們這邊applicationContext.xml 有設定`historyTransactionManager`裡面的`<qualifier value="historyTx"/>`記得設定對應資料

```java=
@Transactional("datasource1Tx")
```



## HibernateTransactionManager LocalEntityManagerFactoryBean 差異
https://blog.supercalifragilisticexpialidociouser.com/page/2/

[yogi0209/spring-mvc-multi-datasource](https://github.com/yogi0209/spring-mvc-multi-datasource)

尚未整理