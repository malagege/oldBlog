---
title: Java 映射整理整理
date: 2022-02-13 23:41:22
tags: [java]
categories: Java
---

程式整理

<!--more-->

## 範例

### 假Bean(含有Map) to 真Bean

1. 靜態(static)方法回傳泛型
2. 

```java=
	public static void copyDataObjectToBean(DataObject src, DataObject dest) throws IntrospectionException, IllegalAccessException, InvocationTargetException, NoSuchMethodException {
		Map map = src.toMap();
		
		Set set = map.keySet();
		Iterator<String> it = set.iterator();
		PropertyDescriptor[] propertiesStr = PropertyUtils.getPropertyDescriptors(dest);
		System.out.println("Map:"+map);
		for(PropertyDescriptor propertyDescriptor : 
		    Introspector.getBeanInfo(dest.getClass()).getPropertyDescriptors()){
		    // propertyEditor.getReadMethod() exposes the getter
		    // btw, this may be null if you have a write-only property
			Method method = propertyDescriptor.getReadMethod();
			String methodStr = propertyDescriptor.getReadMethod().getName().substring(3);
		    String beanKey = propertyDescriptor.getReadMethod().getName().substring(3).toUpperCase();
		    String name =  underscoreName(propertyDescriptor.getName());
		    if(!map.containsKey(name)) {
		    	continue;
		    }
		    String value = MapUtils.getString(map, name);
		    
//		    System.out.println("beanKey:" + propertyDescriptor.getName());

		    BeanUtils.setProperty(dest, propertyDescriptor.getName(), value);
		}
	}

	public static <T extends DataObject> T copyDataObjectToBean(DataObject src, Class<T> clazz) throws IntrospectionException, IllegalAccessException, InvocationTargetException, NoSuchMethodException, InstantiationException {
		Map map = src.toMap();
		
		Set set = map.keySet();
		Iterator<String> it = set.iterator();
		DataObject dest = clazz.newInstance();
		PropertyDescriptor[] propertiesStr = PropertyUtils.getPropertyDescriptors(dest);
		System.out.println("Map:"+map);
		for(PropertyDescriptor propertyDescriptor : 
			
		    Introspector.getBeanInfo(dest.getClass()).getPropertyDescriptors()){
		    // propertyEditor.getReadMethod() exposes the getter
		    // btw, this may be null if you have a write-only property
			Method method = propertyDescriptor.getReadMethod();
			String methodStr = propertyDescriptor.getReadMethod().getName().substring(3);
		    String beanKey = propertyDescriptor.getReadMethod().getName().substring(3).toUpperCase();
		    String name =  underscoreName(propertyDescriptor.getName());
		    if(!map.containsKey(name)) {
		    	continue;
		    }
		    String value = MapUtils.getString(map, name);
		    
//		    System.out.println("beanKey:" + propertyDescriptor.getName());

		    BeanUtils.setProperty(dest, propertyDescriptor.getName(), value);
		}
		return (T) dest;
	}
	
    /**
     * 將駝峰式命名的字串轉換為下劃線大寫方式。如果轉換前的駝峰式命名的字串為空，則返回空字串。</br>
     * 例如：HelloWorld->HELLO_WORLD
     * @param name 轉換前的駝峰式命名的字串
     * @return 轉換後下劃線大寫方式命名的字串
     */
    public static String underscoreName(String name) {
        StringBuilder result = new StringBuilder();
        if (name != null && name.length() > 0) {
            // 將第一個字元處理成大寫
            result.append(name.substring(0, 1).toUpperCase());
            // 迴圈處理其餘字元
            for (int i = 1; i < name.length(); i++) {
                String s = name.substring(i, i + 1);
                // 在大寫字母前新增下劃線
                if (s.equals(s.toUpperCase()) && !Character.isDigit(s.charAt(0))) {
                    result.append("_");
                }
                // 其他字元直接轉成大寫
                result.append(s.toUpperCase());
            }
        }
        return result.toString();
    }

    /**
     * 將下劃線大寫方式命名的字串轉換為駝峰式。如果轉換前的下劃線大寫方式命名的字串為空，則返回空字串。</br>
     * 例如：HELLO_WORLD->HelloWorld
     * @param name 轉換前的下劃線大寫方式命名的字串
     * @return 轉換後的駝峰式命名的字串
     */
    public static String camelName(String name) {
        StringBuilder result = new StringBuilder();
        // 快速檢查
        if (name == null || name.isEmpty()) {
            // 沒必要轉換
            return "";
        } else if (!name.contains("_")) {
            // 不含下劃線，僅將首字母小寫
            return name.substring(0, 1).toLowerCase() + name.substring(1);
        }
        // 用下劃線將原始字串分割
        String camels[] = name.split("_");
        for (String camel :  camels) {
            // 跳過原始字串中開頭、結尾的下換線或雙重下劃線
            if (camel.isEmpty()) {
                continue;
            }
            // 處理真正的駝峰片段
            if (result.length() == 0) {
                // 第一個駝峰片段，全部字母都小寫
                result.append(camel.toLowerCase());
            } else {
                // 其他的駝峰片段，首字母大寫
                result.append(camel.substring(0, 1).toUpperCase());
                result.append(camel.substring(1).toLowerCase());
            }
        }
        return result.toString();
    }

```


### 網路範例


```java=
for(PropertyDescriptor propertyDescriptor : 
    Introspector.getBeanInfo(yourClass).getPropertyDescriptors()){

    // propertyEditor.getReadMethod() exposes the getter
    // btw, this may be null if you have a write-only property
    System.out.println(propertyDescriptor.getReadMethod());
}
```
[[Solved] Java Reflection: How can I get the all getter methods of a java class and invoke them - Code Redirect](https://coderedirect.com/questions/89538/java-reflection-how-can-i-get-the-all-getter-methods-of-a-java-class-and-invoke)


## 理論整理

[Spring的BeanUtils實現忽略大小寫的copyProperties（）方法！ - 台部落](https://www.twblogs.net/a/5db29608bd9eee310ee624c7)


[关于属性描述符PropertyDescriptor - 猫叔的博客 | MySelf](https://unclecatmyself.github.io/2019/01/19/propertyDescriptor/)

[详叙BeanWrapper和PropertyDescriptor - YourBatman - 博客园](https://www.cnblogs.com/yourbatman/p/11212258.html


[小信豬的原始部落: Java 學習筆記 (10) - Reflection](http://godleon.blogspot.com/2007/09/class-class-java-class-class-jvm-class.html?m=1)


[菜鳥工程師 肉豬: Java 使用Class.forName().newinstance()產生物件實例。](https://matthung0807.blogspot.com/2018/11/java-classfornamenewinstance.html?m=1)