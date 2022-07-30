---
title: ElasticSearch sql 查詢改寫原生語法
date: 2022-07-31 01:02:04
tags: [ElasticSearch]
categories: ElasticSearch
---

最進 ES 升級原本 SQL不能用，這邊改寫原生語法，小記一下。

<!--more-->

## 改寫

### 原程式

```java=
	public List<String> findForES(String indexName, String beginColumnName, String where) throws Exception {
		
		HttpClient client = null;
		HttpPost httpPost = null;
		HttpResponse response = null;
		StringBuffer url = new StringBuffer();
		String result = "";
		List<String> resultList = null;
		try {
			client = HttpClients.custom().build();
			url.append(ELASTICSEARCH_URL + "_sql?sql=");
			url.append("select " + beginColumnName);
			url.append("  from " + indexName.toLowerCase() + "/" + ELASTICSEARCH_TYPE );
			url.append(where);
			logger.info("ES SQL:" + url.toString());
			httpPost = new HttpPost(url.toString().replaceAll(" ", "%20"));
			httpPost.addHeader("Content-Type", ES_CONTENT_TYPE_JSON);
			response = client.execute(httpPost);
			if(response.getStatusLine().getStatusCode() == 200) {
				result = EntityUtils.toString(response.getEntity(), "UTF-8");
				if(StringUtils.isNotBlank(result)) {
					resultList = JsonUtil.jsonSkipToList(result);
				}
			}
				
		} catch (Exception e) {
			e.printStackTrace();
			throw new Exception(e);
		}
		
		return resultList;
	}
```

### 改寫程式

此程式參考用就好，尚未不知道有什麼缺陷，請小心使用

```java=
	public List<String> findForES(String index, String[] include, QueryBuilder queryBuilder) throws Exception {
		SearchRequest searchRequest = new SearchRequest(index);
		SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
		if(include != null){
			searchSourceBuilder.fetchSource(include, null);
		}
		searchSourceBuilder.query(queryBuilder == null ? QueryBuilders.matchAllQuery() : queryBuilder);
		searchSourceBuilder.from(0).size(5000);
		searchSourceBuilder.timeout(new TimeValue(60, TimeUnit.SECONDS));
		searchRequest.source(searchSourceBuilder);
		SearchResponse response = client.search(searchRequest, RequestOptions.DEFAULT);
		List<String> list = new ArrayList<>();
		response.getHits().forEach(hit -> list.add(hit.getSourceAsString()));
		return list;
	}

```

## 參考文章

[ElasticSearch的基本原理与用法 - Boblim - 博客园](https://www.cnblogs.com/fnlingnzb-learner/p/10749509.html)

[彻底搞懂 Elasticsearch Java API - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/article/1459354)


[使用Java Low Level REST Client操作elasticsearch - ~冰 - 博客园](https://www.cnblogs.com/ginb/p/8682092.html)


[High Level REST Client（6.3.x）](https://help.aliyun.com/document_detail/131254.html)


[[Elasticsearch] 深入搜索 | 小信豬的原始部落](https://godleon.github.io/blog/Elasticsearch/Elasticsearch-advanced-search/)

https://blog.csdn.net/hudeyong926/article/details/120059127


https://www.elastic.co/guide/en/elasticsearch/client/java-rest/6.3/java-rest-high-query-builders.html

https://blog.51cto.com/xpleaf/2294268


https://iter01.com/612169.html

https://www.cnblogs.com/yjmyzz/p/elasticsearch_7_tutorial_2.html


https://cloud.tencent.com/developer/article/1833288

https://cloud.tencent.com/developer/article/1833288


https://www.cnblogs.com/ginb/p/8716485.html


