---
title: "php switch, if, 陣列套用方法比較"
date: 2019-07-04 23:52:50
tags: [if, switch, if]
categories: PHP
---

最近聽到 PHP 盡量不要用 switch 做條件
用 array 方式比較快!!
網路上很多都是比較 switch, if
當下就好奇了一下，修改遺下看秒數

<!--more-->

## if,switch 比較

[Jiahe Jou: [轉錄] PHP - if v.s. switch 誰快?](http://joujiahe.blogspot.com/2011/09/php-if-vs-switch.html)

當下修正
[Online PHP editor | output for t8CM0](https://3v4l.org/t8CM0)

```
Output for 7.3.5
    Performance Testing PHP if / else VS switch with 100,000 iterations

    total if / else time: 0.007087230682373
    total case time: 0.0072040557861328
    total array time: 0.006603479385376
```

```
Output for 5.6.0
    Performance Testing PHP if / else VS switch with 100,000 iterations

    total if / else time: 0.035436391830444
    total case time: 0.026345729827881
    total array time: 0.046525478363037
```

可以看出 PHP 7 陣列效能真的快很多了

不過，我覺得還是要看情況用
不然失去程式可讀性也不是很好
