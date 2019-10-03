---
title: ISO 8601 duration 時間標準小記
date: 2019-10-01 22:48:38
tags: [duration]
categories: 程式心法
---

最近發現串接的 API 有這種格式`"P3Y6M4DT12H30M5S"`
第一次看到這種好奇是什麼東西
發現是一個標準格式 **ISO 8601 duration**

<!--more-->

```
PnYnMnDTnHnMnS
PnW
P<date>T<time>
```

> "P3Y6M4DT12H30M5S" 就表示 "3年6個月4天12小時又30分5秒"，

看這麼一連串字，覺得很難看到
但其實舊時一個英文搭配數字組合方式

## 現成 Library

### momentjs

[Moment.js | Docs](https://momentjs.com/docs/#/durations/)

```javascript
moment.duration('P3Y6M4DT12H30M5S','minutes').toString();
"P3Y6M4DT12H30M5S"
moment.duration('P3Y6M4DT12H30M5S','minutes').humanize()
"4 years"
moment.duration('P3Y6M4DT12H30M5S','minutes').minutes()
30
moment.duration('P3Y6M4DT12H30M5S','minutes').as('hours')
30780.50138888889
moment.duration('P3Y6M4DT12H30M5S','minutes').toISOString()
"P3Y6M4DT12H30M5S"
```

[Duration missing features · Issue #463 · moment/moment](https://github.com/moment/moment/issues/463)

#### 換成好看的時間

```javascript
moment.utc(moment.duration('P3Y6M4DT12H30M5S').asMilliseconds()).format("HH:mm");
```

[Format a moment duration as Hours and Minutes · TCS Blog](https://blog.tcs.de/format-a-moment-duration-as-hours-and-minutes/)

### Java


java.time.Duration

[生活記事簿: Parse ISO 8601 duration 的方法](http://hklifenote.blogspot.com/2016/03/parse-iso-8601-duration.html)


### PHP

[PHP: DateInterval::__construct - Manual](https://www.php.net/manual/en/dateinterval.construct.php)

```php
<?php

$interval = new DateInterval('P2Y4DT6H8M');
var_dump($interval);
```

[PHP: DateInterval::format - Manual](https://www.php.net/manual/en/dateinterval.format.php)


另外 DB 要怎麼紀錄這種資料呢?
[What MySQL datatype to store a PHP DateInterval - Stack Overflow](https://stackoverflow.com/questions/42837852/what-mysql-datatype-to-store-a-php-dateinterval)
這邊裡面有提到用 varchar
但我覺得存 int 會比較好

先記錄到這邊，有發現好用的在紀錄