---
layout: post
title: '[Python]文件儲存unicode碼的問題'
date: 2014-11-19 06:50
comments: true
categories: Python
---
由於用網頁爬蟲抓取的資料存入json
文件都會顯示`"{artist": "\u3058\u3093"}`
但程式抓取都不會有亂碼的問題

但今天我Google大神一個整天
終於找到方法
```python
str.encode().decode('unicode_escape')
```
<!--more-->


儲存文件時請記得調成**utf-8**
```python
fp = open("animeList.json","w+", encoding = "utf-8")
```

內文的\n,\r,\t...等等都會不見
最終以`str.replace('\n','\\n')`來解決

repr包字串會跑出`\x5e`東西

參考：
http://stackoverflow.com/questions/24787224/how-to-convert-a-python-string
http://www.haogongju.net/art/2041510
http://elleryq-blog.logdown.com/posts/197801/python3-u860bu679c-into-a-unicode-string
