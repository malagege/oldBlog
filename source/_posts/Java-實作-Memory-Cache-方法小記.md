---
title: Java 實作 Memory Cache 方法小記
date: 2021-01-07 23:15:17
tags: [java]
categories: Java
---

最近程式會頻繁撈取資料庫
想有沒有減輕資料庫負擔
後來想到之前 swoole 課程有提到 Memory Cache
今天網路找了一下 Java 實作
發現還滿多實作可以直接使用

<!--more-->

1. CachesExplained

- [CachesExplained · google/guava Wiki · GitHub](https://github.com/google/guava/wiki/CachesExplained)
- [[Google Guava] 3-缓存 | 并发编程网 – ifeve.com](http://ifeve.com/google-guava-cachesexplained/)
- [HashMap? ConcurrentHashMap? 相信看完这篇没人能难住你！ | crossoverJie's Blog](https://crossoverjie.top/2018/07/23/java-senior/ConcurrentHashMap/)

2. [GitHub - lixk/ECache: simple memory cache for Java](https://github.com/lixk/ECache)

簡單實用，我是使用這個範例實作
就專案能用

```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

/**
 * simple memory cache
 *
 * @author lixk
 */
public class ECache {
	/**
	 * key-value data set
	 */
	private final static Map<String, Entity> DATA_BASE = new HashMap<>();
	/**
	 * use scheduled executor to clean expired data
	 */
	private final static ScheduledExecutorService CLEANER = Executors.newSingleThreadScheduledExecutor();

	/**
	 * put data to cache
	 *
	 * @param key   unique identification for data
	 * @param value your data
	 */
	public synchronized static void put(String key, Object value) {
		ECache.put(key, value, 0L);
	}

	/**
	 * put data to cache
	 *
	 * @param key    unique identification for data
	 * @param value  your data
	 * @param expire data survival time in milliseconds
	 */
	public synchronized static void put(final String key, final Object value, final long expire) {
		//clean old data
		ECache.remove(key);
		//set schedule task
		if (expire > 0L) {
			Future future = CLEANER.schedule(new Runnable() {
				@Override
				public void run() {
					//remove data
					synchronized (ECache.class) {
						DATA_BASE.remove(key);
					}
				}
			}, expire, TimeUnit.MILLISECONDS);
			DATA_BASE.put(key, new Entity(value, expire, future));
		} else {
			DATA_BASE.put(key, new Entity(value, null, null));
		}
	}

	/**
	 * read data from cache
	 *
	 * @param key unique identification for data
	 * @return data bound to the key
	 */
	public synchronized static <T> T get(String key) {
		Entity entity = DATA_BASE.get(key);
		if (entity != null && entity.isAlive()) {
			return (T) entity.value;
		}
		return null;
	}

	/**
	 * remove cached data
	 *
	 * @param key unique identification for data
	 * @return data bound to the key
	 */
	public synchronized static <T> T remove(String key) {
		//remove data from data set
		Entity entity = DATA_BASE.remove(key);
		if (entity == null) {
			return null;
		}
		//cancel the schedule task
		if (entity.future != null) {
			entity.future.cancel(true);
		}

		return entity.isAlive() ? (T) entity.value : null;
	}

	/**
	 * cached data entity
	 */
	private static class Entity {
		Object value;
		Long expirationTime;
		Future future;

		Entity(Object value, Long expire, Future future) {
			this.value = value;
			this.expirationTime = (expire == null ? null : System.currentTimeMillis() + expire);
			this.future = future;
		}

		/**
		 * Judging whether the entity is still alive
		 *
		 * @return boolean
		 */
		boolean isAlive() {
			return this.expirationTime == null || this.expirationTime >= System.currentTimeMillis();
		}
	}
}


```

3. [Java in Memory Cache – Geekout – From one propeller head to another!](http://hulen.com/java-in-memory-cache/)

- [利用LRUMap 设计缓存 - mingziday - 博客园](https://www.cnblogs.com/mingziday/p/4910004.html)


彩蛋:

[什么是 istio | Cizixs Write Here](https://cizixs.com/2018/08/26/what-is-istio/)
[【技术教程】利用User-agent-utils获取用户浏览器、操作系统等信息 - 数字人文技术实验室](http://lab1.liulichao.com/2016/08/15/user-agent-utils.html)
[新一代的微服务架构 Service Mesh - SegmentFault 思否](https://segmentfault.com/a/1190000023011928)
[GitHub - qiao-zhi/Spring-Struts-Mybaits-Shiro: Spring+Struts2+Mybatis+Shiro的权限管理](https://github.com/qiao-zhi/Spring-Struts-Mybaits-Shiro)