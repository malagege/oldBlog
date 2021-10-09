---
title: Java Jackson/GSON 處理 JSON 搭配 ArrayList 泛型
date: 2021-10-09 22:56:36
tags:
categories: Java
---

ArrayList 做泛型必須要做特別動作，平常不常做，遇到這個問題需要特別處理。

Jackson 的 JSON 的ObjectMapper 做`.getMapper().getTypeFactory().constructCollectionType`可以解決這個問題。 這個目前套件Jackson 可以做到，GSON好像也能，但這邊就不深入。

<!--more-->

```java
public void test_list_string() {
    try {
        String listString = "[ \"1\",\"2\",\"3\" ]";
        JavaType stringCollection = JsonUtils.getMapper().getTypeFactory().constructCollectionType(List.class, String.class);
        List<String> dataList = JsonUtils.getMapper().readValue(listString, stringCollection);

        dataList.forEach(data -> System.out.println(String.format("data:%s", data)));

    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

[详解 Jackson 中的 ObjectMapper 类中的 readValue 方法对泛型的处理 | ckJava](http://ckjava.com/2020/06/15/Jackson-ObjectMapper-readValue-practice/) ![備份圖](https://i.imgur.com/Azta6Lb.png)

[Jackson反序列化泛型List(使用JavaType将json字符串转换成泛型List) - 高木子 - 博客园](https://www.cnblogs.com/gaomanito/p/9591730.html)


## 綜合比較

[借助Jackson的JsonTypeInfo注解实现多态类的解析_bruce128的专栏-CSDN博客_jsontypeinfo](https://blog.csdn.net/bruce128/article/details/80298808)

## Jackson 最佳處理


```java
tring expected="[{\"a\":12},{\"b\":23},{\"name\":\"Ryan\"}]";
CollectionType listType = mapper.getTypeFactory().constructCollectionType(ArrayList.class, User.class);
List<User> userList = mapper.readValue(expected, listType);
```

[解決ObjectMapper.convertValue() 遇到的一些問題 – WalkonNet](https://walkonnet.com/archives/337448)![備份圖](https://i.imgur.com/I0bzO8m.png)
[line-bot-sdk-java/Message.java at master · line/line-bot-sdk-java](https://github.com/line/line-bot-sdk-java/blob/master/line-bot-model/src/main/java/com/linecorp/bot/model/message/Message.java)
[gson/RuntimeTypeAdapterFactory.java at master · google/gson](https://github.com/google/gson/blob/master/extras/src/main/java/com/google/gson/typeadapters/RuntimeTypeAdapterFactory.java)


## 特別處理
[Jackson Json Subtype Mapping · Leo Niedermeier](https://leoniedermeier.github.io/docs/java/snippets/java_misc/jackson_polymorphic.html)

[【JAVA】从泛型中提取类进行json反序列化_zou15590的博客-CSDN博客](https://blog.csdn.net/zou15590/article/details/95197681)


## GSON 地雷??

[Json反序列化与Java泛型 _weixin_33670786的博客-CSDN博客](https://blog.csdn.net/weixin_33670786/article/details/89538818)![備份圖](https://i.imgur.com/8VFQk9Y.png)
