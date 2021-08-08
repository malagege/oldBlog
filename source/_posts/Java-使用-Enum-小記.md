---
title: Java 使用 Enum 小記
date: 2020-05-28 20:09:16
tags: [java]
categories: Java
---

平常不怎麼使用 Enum ，非常地不熟悉，今天剛好用到，順便記錄一下。

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


## 取出列表資料(values)

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

### values() 神奇 method 實作方法

參考: [Java Enum - javatpoint](https://www.javatpoint.com/enum-in-java)
原理 static 放 `$VALUES` 陣列變數，使用`values()` 會傳 `$VALUES` 陣列變數 ，就可以 foreach 比較。

```java
    final class Season extends Enum  
    {  
        public static Season[] values()  
        {  
            return (Season[])$VALUES.clone();  
        }  
        public static Season valueOf(String s)  
        {  
            return (Season)Enum.valueOf(Season, s);  
        }  
        private Season(String s, int i, int j)  
        {  
            super(s, i);  
            value = j;  
        }  
        public static final Season WINTER;  
        public static final Season SUMMER;  
        private int value;  
        private static final Season $VALUES[];  
        static   
        {  
            WINTER = new Season("WINTER", 0, 10);  
            SUMMER = new Season("SUMMER", 1, 20);  
            $VALUES = (new Season[] {  
                WINTER, SUMMER  
            });  
        }  
    }  
```

其他之後有遇到再補

**2020-06-18**

## 特別載入環境變數方法

最近做 Enum 發現測試環境跟正式環境設定的值會不一樣，所以找一下有什麼方法可以解決這個問題。
目前這邊還沒測試能不能這樣用。

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


## enum 裡面帶 method 方法

```java
 //1.提供当前枚举类的对象，多个对象之间用逗号隔开，末尾对象分号结束
   SPRING ("春天","春暖花开"){
        @Override
        public void show() {
            System.out.println("spring!!!");
        }
    },
   SUMMER("夏天","夏日炎炎"){
       @Override
       public void show() {
           System.out.println("summer!!!");
       }
   },
   AUTUMN ("秋天","秋高气爽"){
       @Override
       public void show() {
           System.out.println("autumn!!!");
       }
   },
   WINTER ("冬天","冰天雪地"){
       @Override
       public void show() {
           super.show();
           System.out.println("winter!!!");
       }
   };
```

參考: [java枚举和注解 - 知乎](https://zhuanlan.zhihu.com/p/355383710)

## 實用範例

方便以後直接找 sample code 來用，畢竟真的不常寫，很容易要想怎麼寫。
最佳範例: [菜鳥工程師 肉豬: Java 列舉(Enum)範例](https://matthung0807.blogspot.com/2017/10/java-enum.html)
還是推薦看裡面內容。

```java
public enum WeekDay {

    SUN("Sunday","星期日", 0),
    MON("Monday","星期一", 1),
    TUE("Tuesday","星期二", 2),
    WED("Wednesday","星期三", 3),
    THU("Thursday","星期四", 4), 
    FRI("Friday","星期五", 5),
    SAT("Saturday","星期六", 6);

    WeekDay(String day, String chinese, int code){
        this.day = day;
        this.chinese = chinese;
        this.code = code;
    }

    private final String day;
    private final String chinese;
    private int code;

    public static WeekDay getWeek(int i){
        for(WeekDay weekDay : values()){
            if(weekDay.getCode() == i) {
                return weekDay;
            }
        }
        return null;
    }

    public String getDay(){
        return this.day;
    }
    
    public String getChinese() {
        return this.chinese;
    }

    public int getCode() {
        return this.code;
    }
  
}
```