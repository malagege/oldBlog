---
title: PHP 產生動態sql技巧(IN)
date: 2018-07-03 20:56:44
tags: [PHP,SQL,IN,NOT IN]
categories: PHP
---

由於mysql處理IN效能不是很好，大量資料做的時候速度會很慢
有用PHP程式動態產生SQL，今天有看到很好用的寫法
筆記一下

<!--more-->

NOTE主要用substr減少最後一個`,`
再放入SQL變數裡面去
寫起來很簡單

```php
<?
$filter = $list = '';    

        if(is_array($array_uid)){
            foreach($array_uid as $user_val){
                $list .= "'".$user_val['uid']."'";
                $list .= ",";
            }
            $list = substr($list, 0, -1);
            $fliter = " AND u.uid NOT IN (".$list.") ";
        }

        $sql = "select * from xxx where 1=1  $filter ";
```


