---
title: Java 使用 Enum 小記
date: 2020-05-28 20:09:16
tags: [java]
categories: Java
---

平常不怎麼使用 Enum 
非常地不熟悉
今天剛好用到
順便季錄一下

<!--more-->

## 簡單使用

```java
public enum Action {
    STOP, RIGHT, LEFT, UP, DOWN
}
```

這邊簡單可以看到
```java
    public static void play(int action) {
        switch(action) {
            case Action.STOP:
                System.out.println("播放停止動畫");
                break;
            略...
            default:
                System.out.println("不支援此動作");
        }
    }
```

以前~~抄抄範例~~，這邊會發現`int action`
怎麼會跑出 `int` 出來？

其實 Enum 反編譯出來，會跟原本 Enum 出來不一樣
不知道這算不算是 Java 語法糖?

```java
    略...
    static {
        STOP = new Action("STOP", 0);
        RIGHT = new Action("RIGHT", 1);
        LEFT = new Action("LEFT", 2);
        UP = new Action("UP", 3);
        DOWN = new Action("DOWN", 4);
        略...
    }
```


詳細內容可以看:[使用 enum 列舉常數](https://openhome.cc/Gossip/Java/Enum.html)

## 自定一些內容

我們公司使用不單是這樣
可以設定出


```java
public enum Car {
    MAX_CAR(100,"最高級車"), NORM_CAR(5,"中級車"), MIN_CAR(1,"低級車"); 
    
    private int price;
    private String desc;
    
    private Car(int price, String desc) {
        this.price = price;
        this.desc = desc;
    }

    public int price() {
        return price;
    }
    
    public String desc() {
        return desc;
    }
    
    public static void main(String[] args) {
        for(Car car : Car.values()) {
            System.out.printf("Car(%s, %d, %s)%n",
                  car , car.price() , car.desc());
        }
    }
}
```


這邊看到會對印到 `MAX_CAR(100,"最高級車"), NORM_CAR(5,"中級車"), MIN_CAR(1,"低級車"); `

```java
    private Car(int price, String desc) {
        this.price = price;
        this.desc = desc;
    }
```


其他詳細可看:[進階 enum 運用](https://openhome.cc/Gossip/Java/EnumMore.html)


## 取出列表資料

```java
for(Car car: Car.values())
    System.out.println(car.name());
}
```

`.values()` 可以印出列表出來

那有沒有find 呢?
目前看應該只能用迴圈滾...



轉成陣列方法
> You can do (pre-Java 8):
> ```java
> List<Enum> enumValues = Arrays.asList(Enum.values());
> ```
> or
> ```java
> List<Enum> enumValues = new ArrayList<Enum>(EnumSet.allOf(Enum.class));
> ```
> Using Java 8 features, you can map each constant to its name:
> ```java
> List<String> enumNames = Stream.of(Enum.values())
>                                .map(Enum::name)
>                                .collect(Collectors.toList());
> ```




[Java: Check if enum contains a given string? - Stack Overflow](https://stackoverflow.com/questions/4936819/java-check-if-enum-contains-a-given-string/4936895)

> This should do it:
> ```java
> public static boolean contains(String test) {
> 
>     for (Choice c : Choice.values()) {
>         if (c.name().equals(test)) {
>             return true;
>         }
>     }
> 
>     return false;
> }
> ```
> This way means you do not have to worry about adding additional enum values later, they are > all checked.
> 
> Edit: If the enum is very large you could stick the values in a HashSet:
> ```java
> public static HashSet<String> getEnums() {
> 
>   HashSet<String> values = new HashSet<String>();
> 
>   for (Choice c : Choice.values()) {
>       values.add(c.name());
>   }
> 
>   return values;
> }
> ```
> Then you can just do: values.contains("your string") which returns true or false.



其他之後有遇到再補

**2020-06-18**

最近做 Enum 發現測試環境跟正式環境設定的值會不一樣
所以找一下有什麼方法可以解決這個問題

[java property file as enum - Stack Overflow](https://stackoverflow.com/questions/4908973/java-property-file-as-enum)

```java
public enum Constants {
    PROP1,
    PROP2;

    private static final String PATH            = "/constants.properties";

    private static final Logger logger          = LoggerFactory.getLogger(Constants.class);

    private static Properties   properties;

    private String          value;

    private void init() {
        if (properties == null) {
            properties = new Properties();
            try {
                properties.load(Constants.class.getResourceAsStream(PATH));
            }
            catch (Exception e) {
                logger.error("Unable to load " + PATH + " file from classpath.", e);
                System.exit(1);
            }
        }
        value = (String) properties.get(this.toString());
    }

    public String getValue() {
        if (value == null) {
            init();
        }
        return value;
    }

}
```
