---
layout: post
title: '[Python]dict排序筆記'
date: 2015-05-04 02:54
comments: true
categories: Python
---
最近突然想到以前有在網路看到Dict做排序
覺得這些以後還用的到
所以記錄一下...

```python
#http://www.saltycrane.com/blog/2007/09/how-to-sort-python-dictionary-by-keys/
for key in sorted(mydict.iterkeys()):
    print "%s: %s" % (key, mydict[key])

for key, value in sorted(mydict.iteritems(), key=lambda (k,v): (v,k)):
    print "%s: %s" % (key, value)
```

寫成現有method
```python
#http://code.activestate.com/recipes/52306-to-sort-a-dictionary/
    # (IMHO) the simplest approach:
    def sortedDictValues1(adict):
        items = adict.items()
        items.sort()
        return [value for key, value in items]

    # an alternative implementation, which
    # happens to run a bit faster for large
    # dictionaries on my machine:
    def sortedDictValues2(adict):
        keys = adict.keys()
        keys.sort()
        return [dict[key] for key in keys]

    # a further slight speed-up on my box
    # is to map a bound-method:
    def sortedDictValues3(adict):
        keys = adict.keys()
        keys.sort()
        return map(adict.get, keys)
```
```python
#http://falldog7.blogspot.com/2009/10/python-dictionary-or-classitemlistsort.html
    d= [ {'id':0, 'value':9}, {'id':1, 'value':100}, {'id':5, 'value':99}, {'id':3, 'value':19}, {'id':2, 'value':59} ]
    d.sort(key=lambda x:x['id']) #針對key 'id' 做sort
    d.sort(key=lambda x:x['value']) #針對key 'value' 做sort

    #以下用cmp，可以達到上面指定key的效果
    d.sort(cmp=lambda x,y: cmp(x['id'], y['id']))
    d.sort(cmp=lambda x,y: cmp(x['value'], y['value']))

    class data:
        def __init__(self, _id, _value):
            self.id    = _id
            self.value = _value
    c = [ data(0,9), data(1,100), data(5,99), data(3,19), data(2,59) ]
    c.sort(key=lambda x:x.id)#針對class member id 做sort
    c.sort(key=lambda x:x.value)#針對class member value 做sort

    #以下用cmp，可以達到上面指定key的效果
    c.sort(cmp=lambda x,y: cmp(x.id, y.id) )
    c.sort(cmp=lambda x,y: cmp(x.value, y.value))
```



資料來源：http://www.dotblogs.com.tw/rickyteng/archive/2011/12/29/63734.aspx