---
layout: post
title: '[Python]使用requests、BeautifulSoup當網頁爬蟲'
date: 2014-11-18 05:40
comments: true
categories: Python
tags: [爬蟲,requests,BeautifulSoup]
---
上次參加COSCUP 2014時候，[電腦不只會幫你選土豆，還會幫你選新聞](http://www.slideshare.net/daikeren/news-37154830)
最近無聊做個小實驗
使用requests、BeautifulSoup實作抓資料存成json
結果發現不會太難

<!--more-->

```a.py
from selenium import webdriver
from bs4 import BeautifulSoup
import requests
import re
import json

#browser = webdriver.Firefox()
#browser.get("/* url */")
#html_source = browser.page_source



soup = BeautifulSoup(requests.get("/* url */").text)


fp = open("/* you can not know it o.< */","w+")
fp.write(json.dumps(b))
fp.close()

```

selenium通常是用來程式測試上面
但也可以抓取網頁HTML
通常用爬蟲requests和beautifulsoup就夠方便了XD