---
title: Hibernate 交易事務失效
date: 2020-08-27 21:54:26
tags: [hibernate, transactional]
categories: Java
---

最近接觸 Spring 專案搭配使用 Hibernate
但是使用上常常處理事務跟我想要的結果不一樣
這個沒有研究清楚，常常事務那邊會一直卡住
最近剛好查到資料，這邊整理一下吧

<!--more-->


- [一個@Transaction哪裡來這麼多坑？ - 程序員DMZ - 博客園](https://www.cnblogs.com/daimzh/p/13532990.html) {% asset_link web1.png 備份圖 %}
- [玩转Spring —— 消失的事务 - 知乎](https://zhuanlan.zhihu.com/p/38208248) {% asset_link web2.png 備份圖 %}

## 主要原因

最近有時候發現交易沒有生效
最近翻到這篇文章
原來需要注意這個部分

這兩篇寫得很棒
簡單總結一下

{% asset_img a.jpg Transactional  %}

上圖簡單說明，使用 Transactional 會用代理模式開啟 DB 事務
但為什麼第一層 Transactional 方法呼叫同個類別(Class)的方法會沒有新開事務呢
會看到代理(Proxy)類別呼叫第二層是呼叫到原本的(不是 Proxy)類別的 method
所以...當然就沒有代理

這時候在想要怎麼新開獨立事務
第一篇寫得很清楚

[玩轉Spring —— 消失的事務 - 知乎](https://zhuanlan.zhihu.com/p/38208248) 這篇文章就是講剛剛的問題，但沒有講要怎麼解決，最後我找到一篇有說要怎麼解決
[一個@Transaction哪裡來這麼多坑？ - 程序員DMZ - 博客園](https://www.cnblogs.com/daimzh/p/13532990.html#业务代码层面)業務代碼層面這邊有程式碼例子，並且解決方法

第二個例子
```java
@Service
public class DmzService {
	@Transactional
	public void save(A a, B b) {
		saveB(b);
	}
	
	@Transactional(propagation = Propagation.REQUIRES_NEW)
	public void saveB(B b){
		dao.saveB(a);
	}
}
```

兩個@Transactional，saveB 不會新開事務
我覺得這個跟第一個原因很像
因為 AOP 代理 第一層做，但是this.saveB 會抓到原本類別 saveB method
**之前學 Spring 有聽過一個口訣，@Transactional 會抓外面優先執行**

最近也有查到@Transactional class和 method，會優先抓 method 的@Transactional 
沒使用 public method 也會失敗，原因是 AOP 的方法需要 public 

**要做事務處理建議使用 XXX.save()**
這邊 XXX 是指 Spring 注入的對象
這跟下面解決方式一樣

> Spring默認拋出了未檢查unchecked異常（繼承自 RuntimeException 的異常）或者 Error才回滾事務；其他異常不會觸發回滾事務，已經執行的SQL會提交掉。如果在事務中拋出其他類型的異常，但卻期望 Spring 能夠回滾事務，就需要指定 rollbackFor屬性。

這邊之後有時間在探討這個問題，這篇只先記錄事務處理

問題來了，這些要怎麼解決?

## 解決

[一個@Transaction哪裡來這麼多坑？ - 程序員DMZ - 博客園](https://www.cnblogs.com/daimzh/p/13532990.html#业务代码层面)下免有兩個方法

1. 自己注入自己
所有 AOP 需要特別建立事務，需要用這個方法比較不會遇到問題

```java
@Service
public class DmzService {
	// 自己注入自己
	@Autowired
	DmzService dmzService;
	
	@Transactional
	public void save(A a, B b) {
		dmzService.saveB(b);
	}

	@Transactional(propagation = Propagation.REQUIRES_NEW)
	public void saveB(B b){
		dao.saveB(a);
	}
}
```

2. 正規方法

```java
@Service
public class DmzService {

	@Transactional
	public void save(A a, B b) {
		((DmzService) AopContext.currentProxy()).saveB(b);
	}

	@Transactional(propagation = Propagation.REQUIRES_NEW)
	public void saveB(B b){
		dao.saveB(a);
	}
}
```


詳細看這兩篇，解決我在 Spring 交易事務的疑惑
沒這兩篇的話，我可能就無法還技術債

## 相關交易處理
這邊也說得很細
[Spring 中的事務傳播 - 個人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000015794446)

希望我有時間再整理這些差別

最近在想有什麼方法可以看到交易事務處理狀態
原本有找到用 Wireshark 抓封包看
看完這篇應該是不需要了 :)

Hibernate 有時起事務會失敗，外層加 @Transactional 就能解決
這邊就放起個小彩蛋連結(我就不整理了)
* [hibernate cascade 的坑 | 头头带你飞](https://toutoudnf.github.io/2016/06/26/java/hibernate%20cascade%20and%20inverse%20usage/)
* [Hibernate4整合spring4報錯----No Session found for current thread - IT閱讀](https://www.itread01.com/p/799954.html)
* [Spring+Hibernate No Session found for current thread异常分析_老王不让用的博客-CSDN博客](https://blog.csdn.net/wangquan1992/article/details/103068150)
* [Spring 中的事務傳播 - 個人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000015794446)
* [终于解决了：No Session found for current thread_lijing_lj928的专栏-CSDN博客](https://blog.csdn.net/lijing_lj928/article/details/50592808)
* [程序员DMZ - 博客园](https://www.cnblogs.com/daimzh/)
* [sprin 事务注解@Transactional的实现原理（转） - myseries - 博客园](https://www.cnblogs.com/myseries/p/12167203.html)
* [技术分享 | 从 wireshark 看 MySQL 8.0 加密连接 - 简书](https://www.jianshu.com/p/a7e57bb92b72)
* [MySQL_通过binlog查看原始SQL语句 - 掘金](https://juejin.im/post/6844903650293186574)
* [jquery 高效实现htmlencode()与htmldecode()函数_jackhuclan的专栏-CSDN博客](https://blog.csdn.net/jackhuclan/article/details/79298039?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.edu_weight&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.edu_weight)
* [Showing a Spring transaction in log4j_王奕然的博客-CSDN博客](https://blog.csdn.net/wyxz126/article/details/8752094)
