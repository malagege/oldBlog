---
layout: post
title: '[Python]圖片轉成base64碼'
date: 2014-11-20 02:36
comments: true
categories: Python
---
實作異常的順力^^

```python image.py
import base64

fp = open("a.jpg","rb")
img = fp.read()
fp.close()

print(base64.b64encode(img))
```

在接下來使用requests來實作

```python image.py
	imgdata = requests.get(imgurl).content
	imgb64 = base64.b64encode(imgdata)
```

  完成:D