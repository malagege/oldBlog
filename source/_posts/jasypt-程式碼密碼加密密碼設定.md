---
title: Jasypt 程式碼密碼加密密碼設定
date: 2022-02-14 00:02:39
tags: [jasypt,java]
categories: Java
---

最近工作上面源掃掃到密碼 Hard Code 程式，有人提供使用 Jasypt 作加密動作，不過，就算改成從 DB 撈出來也會抓到這個問題，所以用 Jasypt 能不能掃到?我也不是很清楚。

這邊我就簡單做個紀錄。

<!--more-->

## 了解怎麼載入 properties(與標題無關)

這邊跟加密沒有關係，但先了解怎麼從 Spring 載入 properties


### 設定



#### 方法一 (context:property-placeholder )

```xml=
<context:property-placeholder location="classpath:system.properties" />
```

> `<context:property-placeholder>`標簽提供了一種優雅的外在化參數配置的方式，不過該標簽在Spring配置文件中只能存在一份！！！

> 眾所周知，Spring容器是采用反射掃描的發現機制，通過標簽的命名空間實例化實例，當Spring探測到容器中有一個org.springframework.beans.factory.config.PropertyPlaceholderCVonfigurer的Bean就會停止對剩余PropertyPlaceholderConfigurer的掃描，即只能存在一個實例！

參考: [Spring配置文件<context:property-placeholder>標簽使用漫談 - IT閱讀](https://www.itread01.com/content/1509782321.html)

#### 方法二 (PropertyPlaceholderConfigurer)  本篇重點!!

```xml=
    <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <!-- 對於讀取一個配置檔案採取的方案 -->
        <!--<property name="location" value="classpath:db.properties"/>-->

        <!--對於讀取多個配置檔案採取的方案-->
        <property name="locations">
            <list>
                <value>classpath:db.properties</value>
                <value>classpath:db2.properties</value>
            </list>
        </property>
    </bean>
```

這邊Jaspty 會用這個!! Jaspty 裡面的 EncryptablePropertyPlaceholderConfigurer 繼承這個物件，我們可以使用`@value`去抓出資料。

#### 更多方法

1. [五种方式让你在java中读取properties文件内容不再是难题 - 阿豪聊干货 - 博客园](https://www.cnblogs.com/hafiz/p/5876243.html) TODO: 備份
2. [spring- properties 讀取的五種方式 | IT人](https://iter01.com/69343.html)
3. [Java程式碼中獲取配置檔案(config.properties)中內容的兩種方法 - IT閱讀](https://www.itread01.com/content/1550515696.html)
4. [Spring註解@Value及屬性載入組態檔方式 - IT145.com](https://www.it145.com/9/161940.html)

### 讀取值方法


#### @value

```java=
    @Value("${item.specKey}")
    private String SPEC_KEY ;
    
    @Value("${item.saltvalueStr}")
	private String SALTVALUE_STR ;
```
#### 讀取方法


```xml=
<bean id="xxx" class="xxxxx">
        <property name="SPEC_KEY" value="${item.specKey}" />
        <property name="SALTVALUE_STR" value="${item.saltvalueStr}" />
    </bean>
```
properties
```
item.specKey=Test
item.saltvalueStr=Test
```


## Jasypt 加密動作

jasypt
```xml=
        <dependency>
            <groupId>org.jasypt</groupId>
            <artifactId>jasypt</artifactId>
            <version>1.9.3</version>
        </dependency>
```


```xml=
   <bean id="jasyptConf" class="org.jasypt.encryption.pbe.config.EnvironmentStringPBEConfig">

        <property name="algorithm" value="PBEWithMD5AndDES" />

        <property name="password" value="********" />

    </bean>

    

    <bean id="jasyptEncryptor" class="org.jasypt.encryption.pbe.StandardPBEStringEncryptor">

        <property name="config" ref="jasyptConf" />

    </bean>

 

    <bean id="propertyConfigurer" class="org.jasypt.spring4.properties.EncryptablePropertyPlaceholderConfigurer">

        <constructor-arg ref="jasyptEncryptor" />

        <property name="locations">

            <list>

                <value>classpath*:com/project/npp/persistence/cfg/jdbc.properties</value>

            </list>

        </property>

        <property name="ignoreUnresolvablePlaceholders" value="true"/>

    </bean>


```


```
jdbc.driverClassName=com.ibm.db2.jcc.DB2Driver

jdbc.username=nppuser1

jdbc.password=ENC(fAObzokoh7ldKBiVU16pqSdPeURziFjb)
```


## 如何產生 Jasypt 加密key?

官網抓的jar 檔，下已下指令可以得出

```bash=

java -cp jasypt-1.9.3.jar org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI input="myPassword" password=privatekey algorithm=PBEWithMD5AndDES
```

![](https://i.imgur.com/gZBKC3e.png)


還有一個方法是用官方 shell script 去產生[Releases · jasypt/jasypt · GitHub](https://github.com/jasypt/jasypt/releases)，這邊就不特別做紀錄，網路很多是用這個方法。


