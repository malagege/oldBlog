---
title: 手動配置 XML 注入 Bean 方法
date: 2020-05-16 23:11:02
tags: [spring]
categories: Java
---

這邊主要是講手動注入

<!--more-->

## 一般 Spring 注入配置文件

一般通常都放在 `/resource/applicationContext.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd 
    http://www.springframework.org/schema/context 
    http://www.springframework.org/schema/context/spring-context.xsd">
     
     <bean ...>
</beans>
```


## 一般配置 bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd 
    http://www.springframework.org/schema/context 
    http://www.springframework.org/schema/context/spring-context.xsd">
     
     <bean class="com.xxx.person.Student" />
</beans>
```

`context.getBean(Student.class)` 可以順利被載入

## 載入是同一多個class 的 bean

```xml
<bean class="com.xxx.person.Student" />
<bean class="com.xxx.person.Student" />
```

一般 `<bean class="com.xxx.person.Student" />` 沒有定義 id 和 name
所以配置出來 Bean 名稱會是 `com.xxx.person.Student#0`
第二個就為`com.xxx.person.Student#1`

**這邊跟自動注入出來名稱不一樣(JavaConfig 名稱為 xxxXxxxXxx ex:redCar )**

這時候 Java 呼叫 Bean 是透過 `(Student)context.getBean("com.xxx.person.Student#0");`
這邊 getBean 使用**字串**記得要強制轉型
不然 Java 認不出來

設為 id or name 如下

```xml
<bean id="student1" class="com.xxx.person.Student" />
<bean id="student2" class="com.xxx.person.Student" />
```

`(Student)context.getBean("student1")`

## 使用 JUnit

這邊一樣用兩個 
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class AppTest{

    @Autowired
    private Student student1;

    @Autowired
    private Student student2;
}
```
這邊注意 student1 的對應 bean id  XML 這一行`<bean id="student1" class="com.xxx.person.Student" />`
名稱要跟xml 配置一致

**複習自動注入是看 class 名稱(xxxXxxx)**

### id 和 name 差別

```xml
<bean name="student1_1 student1_2" class="com.xxx.person.Student" />
<bean name="student2" class="com.xxx.person.Student" />
```

`name 可以多個命名`，如上第一行 student1_1 student1_2 可用空白、逗號、加號
id 就不行，但也不是說不能這樣寫`<bean name="student1_1 student1_2" class="com.xxx.person.Student" />`
只是這邊是指 `student1_1 student1_2` 是一個名稱
所以這邊就沒辦法 @Autowired 方式

```java
@Autowired
private Student student1_1 student1_2;
```

要這樣使用`(Student) context.getBean("student1_1 student1_2");`

## 建構式注入方法

1. `<constructor-arg>`
2. c-名稱空間入住(Spring 3.0 引進)

### constructor-arg tag

```xml
<bean id="seat1" class="com.xxx.Seat">
    <constructor-arg name="student" ref="student1" />
</bean>
```

這邊的 name 是指 建構式的參數`Seat (Student student)`

```Java
public class Seat{

    private Student st;

    public Seat(Student student){
        this.st = student;
    }
}
```

#### 細說(可能用不到)

```xml
<!-- value 可以放值 -->
<constructor-arg name = "name" value = "tom"></constructor-arg>
<!-- null -->
<constructor-arg name = "name">
<null></null>
</constructor-arg>
<!-- -->
<constructor-arg type="int" value="2001"/>
<constructor-arg type="java.lang.String" value="Zara"/>
<!-- index -->
<constructor-arg index="0" value="2001"/>
<constructor-arg index="1" value="Zara"/>

```

Spring 3 開始好像才能用建構式的參數
[java - Spring - Attribute 'name' is not allowed to appear in element 'constructor-arg' - Stack Overflow](https://stackoverflow.com/questions/13669798/spring-attribute-name-is-not-allowed-to-appear-in-element-constructor-arg)
在之前版本應該都是用 index 方式

> （1）set注入：優點降低了耦合程度，可以按需生成set方法進行傳值；缺點：當需要注入的參數很多時，會有很多的set方法
> （2）構造方法注入：增加了耦合程度，因為默認的無參的構造方法被覆蓋了；不能按需傳參；
> sping官方推薦：使用set注入方法
參考: [Spring學習筆記二：構造方法注入](https://gist.github.com/xuxiying/3193537)



### C 名稱空間注入

先說 c 名稱空間跟 p 名稱空間 沒關係

#### 宣告方法

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:c="http://www.springframework.org/schema/c"
```

#### 建構子注入

```xml
<bean id="seat1" class="com.xxx.Seat" c:st-ref="student1"></bean>
```

這邊可以換成位置 c:_0-ref="student1"
但我覺得用 name 比較實用

非 Bean 的值可以用
c:_1="201799777799" c:_2-ref="notify"

##### 使用名稱注入

這邊 bean 是我邊的
```xml
<bean id="seat1" class="com.xxx.Seat" c:teacherName="john"></bean>
```
xml 是區分大小寫的
[XML解析忽略标签名的大小写_java_一直在路上-CSDN博客](https://blog.csdn.net/wang_yunj/article/details/43795269)


### 建構式注入集合(collections)

#### list

```xml
<!--字串-->
<constructor-arg name = "name">
    <list>
        <value>Hello 1</value>
        <value>Hello 2</value>
        <value>Hello 2</value>
    </list>
</constructor-arg>

<!-- bean -->
<constructor-arg name = "name">
    <list>
        <ref bean="{Bean_id}" />
        <ref bean="{Bean_id}" />
        <ref bean="{Bean_id}" />
        <ref bean="student1" />
    </list>
</constructor-arg>

```

#### Set

```xml
<!--字串-->
<constructor-arg name = "name">
    <set>
        <value>Hello 1</value>
        <value>Hello 2</value>
        <value>Hello 2</value>
    </set>
</constructor-arg>

<!-- bean -->
<constructor-arg name = "name">
    <set>
        <ref bean="{Bean_id}" />
        <ref bean="{Bean_id}" />
        <ref bean="{Bean_id}" />
        <ref bean="student1" />
    </set>
</constructor-arg>

```

一般 Set 在 Java 是無序列排序
但是在 Spring 使用 LinkedHashSet，所以是有排序的
使用 C 名稱無法使用集合(collection)注入

#### map

```xml
<constructor-arg name = "name">
    <map>
        <entry key="m1" value="value1" />
        <entry key="m1" value-ref="{bean_id}" />
    </map>
</constructor-arg>

```

#### array

```xml
<!-- string -->
<constructor-arg name = "name">
    <list>
        <value>Hello 1</value>
        <value>Hello 2</value>
        <value>Hello 3</value>
    </list>
</constructor-arg>

<!-- bean -->
<constructor-arg name = "name">
    <list>
        <ref bean="{Bean_id}" />
        <ref bean="{Bean_id}" />
        <ref bean="{Bean_id}" />
        <ref bean="student1" />
    </list>
</constructor-arg>
```

使用 java 方式
String[] student;
Student[] student;

##  屬性(setter) 注入

```xml
<bean id="student1" class="com.xxx.student">
    <property name="name" value="John" />
</bean>
<bean id="student2" class="com.xxx.student">
    <property name="name" value="Kane" />
</bean>
```

```java
class Student{
    private String name;
    public String setName(String name){
        this.name = name;
    }
}
```

屬性名字如上 name，是跟著 set**Name**一致
假如 method 名稱改成 setName1， xml 配住需要改`<property name="name1" value="Kane">`

### 一般 property 標籤方法

#### array

```xml
<bean id="classroom1" class="com.xxx.classroom">
<property name="students">
    <array>
        <ref bean="student1">
        <ref bean="student2">
    </array>
</property>
</bean>
```

#### list

```xml
<bean id="classroom1" class="com.xxx.classroom">
<property name="students">
    <list>
        <ref bean="student1">
        <ref bean="student2">
    </list>
</property>
</bean>
```

#### set

```xml
<bean id="classroom1" class="com.xxx.classroom">
<property name="students">
    <set>
        <ref bean="student1">
        <ref bean="student2">
    </set>
</property>
</bean>
```

#### object

```xml
<bean id="classroom1" class="com.xxx.classroom">
    <property name="student" ref="student1" />
</bean>
```

### p 命名空間

在 beans 屬性加入`  xmlns:p="http://www.springframework.org/schema/p"`
如:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
```

#### 一般使用方法

```xml
<bean id="student1" class="com.xxx.student" p:name="John">
</bean>
```

#### object

```xml
<bean id="classroom1" class="com.xxx.Classroom" p:student-ref="student1">
</bean>
```

備註
p 命名空間一樣不能用集合(collection)方法

#### util 名稱空間

只能用在 p命名空間

##### 宣告

`xmlns:util="http://www.springframework.org/schema/util"` 
和
```xml
     xsi:schemaLocation="http://www.springframework.org/schema/beans
     http://www.springframework.org/schema/beans/spring-beans-4.1.xsd
     http://www.springframework.org/schema/util
     http://www.springframework.org/schema/util/spring-util-4.1.xsd">
```

結果

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xmlns:util="http://www.springframework.org/schema/util"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
     http://www.springframework.org/schema/beans/spring-beans-4.1.xsd
     http://www.springframework.org/schema/util
     http://www.springframework.org/schema/util/spring-util-4.1.xsd">
```

##### 使用 array 或 list

```xml
<util:list id="studentList">
    <ref bean="student1" />
    <ref bean="student2" />
</util:list>

<bean id="classroom1" class="com.xxx.classroom" p:students-ref="studentList">
<!-- <property name="students">
    <list>
        <ref bean="student1">
        <ref bean="student2">
    </list>
</property> -->
</bean>
```

##### set、map 

```xml
<util:map id="studentList">
    <ref bean="student1" />
    <ref bean="student2" />
</util:map>
```

以此類推

## 複習Spring 那些裝配

1. 自動裝配
2. Java 裝配
3. XML 裝配

## 相關知識

### 預設注入生命週期

```xml
<bean id="student1" class="com.xxx.student" p:name="John">
</bean>
```

```java
public class Student{

    public test1(){
        System.out.println(" Student " + this.toString());
    }
}
```


```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class AppTest{

    @Autowired
    private Student student1;

    @Autowired
    private Student student2;

    @test
    public void test1(){

    }
}
```

```java
public class AppTest{
    public static void main(String[] argv){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

		// Student st1 = (Student)context.getBean("student");
        // Student st2 = (Student)context.getBean("student");

        // System.out.println(st1 == st2); //true
    }
}

```


執行程式會看到 Bean 會啟動，不管會不會呼叫
預設啟動是**單例模式**

### 生命週期

1. 單例(Sinletion)

在整個應用程序中，只創建 bean 的一個實例

2. 原型(Prototype)

每次注入或通過 Spring 上下文獲取的時候，都會創建一個新的 bean 實例
簡單說就是每一次 @Autowired 都會實例出來

3. 會話(Session)

在 Web 應用中，為每個會話創建一個 bean 實例

4. 請求(Request)

在 Web 應用中，為每個請求創建一個 bean 實例


#### 設定生命周期作用域

**scope=**

```xml
<bean id="student1" class="com.xxx.student" p:name="John" scope="prototype">
</bean>
```

## 總整理作用域配置

### xml 配置

scope="singleton"

### 自動裝配

@Component
@Scope("singletion")

### JavaConfig 配置

@Bean
@Scope(ConfigurableBeanFactory.SCOPE_SINGLETON)

## 延遲加載

通常用於單例模式

### xml 配置

lazy-init="true"

### 自動配裝

@Component
@Lazy

### JavaConfig

@Bean
@Lazy

## 初始化方法和銷毀方法

### xml 配置

init-method="init" destory-method="destory"

### 自動配裝和 JavaConfig

```java
@PostConstruct
public void init() { System.out.println("始化方法"); }

@PreDestroy
public void destroy() { System.out.println("銷毀方法"); }
```

## 工廠方法

### 靜態工廠方法

```xml
<bean id="student1" class="com.xxx.Student" factory-method="createStudent1"/>
```

### 實例方法

```xml
<bean id="studentFactory" class="com.xxx.Student" />
<bean id="student2" factory-bean="studentFactory" factory-method="createStudent1" />
```

## props 屬性




其他參考:
[【Spring学习12】XML简写p-namespace及c-namespace_java_程序老兵的博客-CSDN博客](https://blog.csdn.net/soonfly/article/details/68928683)
[菜鳥工程師 肉豬: Spring 注入properties檔的參數設定](https://matthung0807.blogspot.com/2017/11/springproperties.html)
[Joseph 筆記本: Java Spring Framework 筆記 - Bean configutation file 設定](https://josephdotw.blogspot.com/2018/09/java-spring-framework-1-bean.html)
[spring中constructor-arg 构造方法注入 - 红色小宇宙 - 博客园](https://www.cnblogs.com/sanhuan/p/4789054.html)