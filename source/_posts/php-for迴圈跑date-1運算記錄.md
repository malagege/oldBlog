---
title: php for迴圈跑date+1天運算記錄
date: 2018-08-05 18:31:53
tags: [php,date]
categories: PHP
---

最近剛好做要依日期做for迴圈
就試著寫寫看
```php
<?php
for ($now_date = $first_day; $now_date <= $last_day; $now_date = date('Y-m-d', strtotime("$now_date +1 DAY"))){
    ...    
}
```
寫完覺得寫出還滿實用的東西
Google爬一下有什麼解法
瞬間覺得我的code是垃圾orz

<!--more-->

## Stack Overflow寫法

```php
<?php
$begin = new DateTime('2010-05-01');
$end = new DateTime('2010-05-10');

$interval = DateInterval::createFromDateString('1 day');
$period = new DatePeriod($begin, $interval, $end);

foreach ($period as $dt) {
    echo $dt->format("l Y-m-d H:i:s\n");
}
```
[I have 2 dates in PHP, how can I run a foreach loop to go through all of those days? - Stack Overflow](https://stackoverflow.com/questions/3207749/i-have-2-dates-in-php-how-can-i-run-a-foreach-loop-to-go-through-all-of-those-d)


## 官網範例

```php
<?php

$begin = new DateTime( '2012-08-01' );
$end = new DateTime( '2012-08-31' );
$end = $end->modify( '+1 day' );

$interval = new DateInterval('P1D');
$daterange = new DatePeriod($begin, $interval ,$end);

foreach($daterange as $date){
    echo $date->format("Ymd") . "<br>";
}
?>
```

感覺實務上還是竟量用DateTime()物件
modify竟然也有做時間加總運算
DatePeriod可以做到日期跑一遍
真的很方便


## DateInterval
[PHP: DateInterval - Manual](http://php.net/manual/zh/class.dateinterval.php)
DateInterval可以做時間之間運算
使用DateTime裡面的add/sub
最神的還能用在diff
算兩個之間差
詳細可以再看看[PHP: DateTime - Manual](http://php.net/manual/zh/class.datetime.php)裡面

裡面爬到DateInterval沒有負的時間
裡面神人有寫
```php
<?php

    class DateRange extends ArrayIterator
    {

        protected $oDate = null;
        protected $oStartDate = null;
        protected $oEndDate = null;
        protected $oInterval = null;

        public function __construct( DateTime $oStartDate, DateTime $oEndDate, DateInterval $oInterval = null )
        {
            $this->oStartDate = $oStartDate;
            $this->oDate = clone $oStartDate;
            $this->oEndDate = $oEndDate;
            $this->oInterval = $oInterval;
        }

        public function next()
        {
            $this->oDate->add($this->oInterval);
            return $this->oDate;
        }

        public function current()
        {
            return $this->oDate;
        }

        public function valid()
        {
            if ($this->oStartDate > $this->oEndDate)
            {
                return $this->oDate >= $this->oEndDate;
            }
            return $this->oDate <= $this->oEndDate;
        }

    }

$oRange = new DateRange(new DateTime("2013-10-01"), new DateTime("2013-01-01"), DateInterval::createFromDateString("-1 month") );
    foreach ($oRange as $oDate)
    {
        echo $oDate->format("Y-m-d") . "<br />";
    }
```


## DatePeriod
[PHP: DatePeriod - Manual](http://php.net/DatePeriod)裡面的Traversable
可以用foreach，不過php裡面沒寫interface function讓我有點疑惑???

感覺有很多東西可以寫，先筆記紀錄

參考來源:
* [PHP - Loop Between Two Dates - TecAdmin.net](https://tecadmin.net/php-loop-between-two-dates/)
* [PHP: DatePeriod - Manual](http://php.net/DatePeriod)
* [I have 2 dates in PHP, how can I run a foreach loop to go through all of those days? - Stack Overflow](https://stackoverflow.com/questions/3207749/i-have-2-dates-in-php-how-can-i-run-a-foreach-loop-to-go-through-all-of-those-d)
