---
title: Eclipse 設定 JDNI 連資料庫
date: 2021-10-09 23:13:18
tags: [eclipse,java,jndi]
categories: Java
---

Eclipse 設定 JDNI 連資料庫

參考:
- [PostgreSQL MySQL 的 JNDI 資料來源 | 他山教程，只選擇最優質的自學材料](http://www.tastones.com/zh-tw/stackoverflow/tomcat/configuring-a-jndi-datasource/jndi_datasource_for_postgresql__mysql/)
- [java - SEVERE: Unable to create initial connections of pool - tomcat 7 with context.xml file - Stack Overflow](https://stackoverflow.com/questions/22155837/severe-unable-to-create-initial-connections-of-pool-tomcat-7-with-context-xml/22155895)

<!--more-->

## 設定

1. 設定 server.xml 設定 資料連線

```xml
	<Resource name="XXXPool"
	              factory="org.apache.tomcat.jdbc.pool.DataSourceFactory"
	              auth="Container"
	              type="javax.sql.DataSource"
	              username="dbuser"
	              password="dbpassword"
	              url="jdbc:postgresql://192.168.1.1:5444/dbname"
	              driverClassName="org.postgresql.Driver"
	              initialSize="20"
	              maxWaitMillis="15000"
	              maxTotal="75"
	              maxIdle="20"
	              maxAge="7200000"
	              testOnBorrow="true"
	              validationQuery="select 1"
	              />
```

2. 設定 context.xml 

```xml=
  <ResourceLink name="XXXPool"
   global="XXXPool"
   type="javax.sql.DataSource"/>
```

3. 放資料庫連線 jar 到 apache-tomcat-7.0.82\lib
4. 設定 Spring ApplicationContext.xml 的 JNDI 連線資訊


```xml
 	<bean id="dataSource" class="org.springframework.jndi.JndiObjectFactoryBean"> 
		<property name="jndiName"> 
			<value>java:comp/env/XXXPool</value>
		</property>
		<property name="cache" value="false" />	 
	</bean>
```


相關可參考:

- [Name jdbc is not bound in this Context - 坏~牧羊人 - 博客园](https://www.cnblogs.com/520playboy/p/6250748.html)
> 在spring配置文件中引用jdbc/oracleds時，應該前面加上java:comp/env/,全名為java:comp/env/jdbc/oracleds


- [Oracle DataBase JNDI 在 JBoss EAP 6.x 上的設定 @ MISTECH 技術手抄本 :: 痞客邦 ::](https://mistech.pixnet.net/blog/post/397940482-oracle-database-jndi-%E5%9C%A8-jboss-eap-6.x-%E4%B8%8A%E7%9A%84%E8%A8%AD%E5%AE%9A) [備份圖](https://i.imgur.com/jrv621l.png)


- [我們應該瞭解的JNDI資料來源配置_FreeBuf - MdEditor](https://www.gushiciku.cn/pl/2H68/zh-tw)

- [可否說明JNDI的用途為何?! @ Davis' Blog :: 隨意窩 Xuite日誌](https://blog.xuite.net/dvsseed/twblog/136039699)

- [菜鳥工程師 肉豬: Spring JBoss AS 5.1 JNDI Oracle Datasource 連線設定](https://matthung0807.blogspot.com/2017/11/spring-jboss-as-51-jndi-oracle.html)