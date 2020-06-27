---
title: JavaConfig 設定 bean 物件
date: 2020-05-13 22:07:44
tags: [java, spring]
categories: Java
---

這邊不使用自動載入

<!--more-->

## 一般注入

```java
@Configuration
public class ...{
    @Bean
    public UserDao userDaoNormal{
        return new UserDaoNormal();
    }
}
```

## 建構式注入

### 一般方法

```java
@Configuration
public class ...{
    @Bean
    public UserService userServiceNormal(){
        UserDao userDao = userDaoNormal();
        return new UserService(userDao);
    }
}
```

### 更好的方法(不多一個 instance )

```java
@Configuration
public class ...{

    @Bean
    public UserDao userDaoNormal{
        return new UserDaoNormal();
    }

    @Bean
    public UserService userServiceNormal(userDao userDao){
        return new UserService(userDao);
    }
}
```

## setter 注入方法

```java
@Configuration
public class ...{

    @Bean
    public UserDao userDaoNormal{
        return new UserDaoNormal();
    }

    @Bean
    public UserService setUserDao(userDao userDao){
        return new UserService(userDao);
    }
}
```

### 任意方法也可以

```java
@Configuration
public class ...{

    @Bean
    public UserDao userDaoNormal{
        return new UserDaoNormal();
    }

    @Bean
    public UserService prepareUserDao(userDao userDao){
        return new UserService(userDao);
    }
}
```

## 解決衝突方法

自動注入和 JavaConfig 解決方案一致

### @Primary

### Qualifier("xxxXxxx")

@Qualifier("xxXxxx")  宣告 or 參數

@Qualifier("xxxXxxx") 宣告

### @Bean 命名方法

默認 @Bean 方法名稱