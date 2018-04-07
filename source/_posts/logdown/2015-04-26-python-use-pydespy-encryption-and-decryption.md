---
layout: post
title: '[Python]使用pyDes.py動態加密解密'
date: 2015-04-26 07:55
comments: true
categories: Python
---
[pyDes Github](https://github.com/toddw-as/pyDes)

```python test_pyDes.py
from pyDes import *
import base64
import time
import random


data =str(random.random())+"|"+"加密內容"
k = des('DESCRYPT', CBC, '12345675', pad=None, padmode=PAD_PKCS5)
d = k.encrypt(data)
print "Encrypted: %r" % base64.b64encode(d)
print "Decrypted: %r" % k.decrypt(d)
```