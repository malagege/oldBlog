---
title: PHP 陣列實用函式小記
date: 2019-06-29 22:17:28
tags: [php, array]
categories: PHP
---

寫了 PHP 一陣子
PHP array function 非常多
所以不是很熟
最近做案子有用到一些實用的
做一下筆記，方便快速尋找

<!--more-->

## array_chunk

陣列切割多個區塊函式
簡單說明一下，有時候需要多批陣列
這時候就用得到這個函式
`array_chunk ( array $array , int $size [, bool $preserve_keys = FALSE ] ) : array`
第三個參數是否要保留 key
[PHP: array_chunk - Manual](https://www.php.net/manual/en/function.array-chunk.php)

```php
<?php

function partition( $list, $p ) {
    $listlen = count( $list );
    $partlen = floor( $listlen / $p );
    $partrem = $listlen % $p;
    $partition = array();
    $mark = 0;
    for ($px = 0; $px < $p; $px++) {
        $incr = ($px < $partrem) ? $partlen + 1 : $partlen;
        $partition[$px] = array_slice( $list, $mark, $incr );
        $mark += $incr;
    }
    return $partition;
}

$citylist = array( "Black Canyon City", "Chandler", "Flagstaff", "Gilbert", "Glendale", "Globe", "Mesa", "Miami",
                   "Phoenix", "Peoria", "Prescott", "Scottsdale", "Sun City", "Surprise", "Tempe", "Tucson", "Wickenburg" );
print_r( partition( $citylist, 3 ) );

?>
```

剛好官網看到很有趣的寫法
partition 決定個數，依照陣列做切割
感覺有機會用到

## array_column

對二維陣列對裡面第一層抓取 key 資料出來。

`array_column ( array $input , mixed $column_key [, mixed $index_key = null ] ) : array`

感覺很適合 Database 搜尋出來資料，抓取部分欄位。
這時可以用 array_filter,array_unique,array_values 做整理。
相當實用!!

[PHP: array_column - Manual](https://www.php.net/manual/zh/function.array-column.php)

當然用途還滿多了，第一個能對**isset,**get 模術方法去抓資料

## array_count_value

`array_count_values ( array $array ) : array`
統計數組中所有的值

```php
<?php
$array = array(1, "hello", 1, "world", "hello");
print_r(array_count_values($array));
// Array
// (
//     [1] => 2
//     [hello] => 2
//     [world] => 1
// )
?>
```

[PHP: array_count_values - Manual](https://www.php.net/manual/zh/function.array-count-values.php)

## array_diff

`array_diff ( array $array1 , array $array2 [, array $... ] ) : array`

對比 array1 和其他一個或者多個數組，返回在 array1 中但是不在其他 array 裡的值。

目前沒什麼用到，但感覺還是有用處的...

## array_fill

`array_fill ( int $start_index , int $num , mixed $value ) : array`

填充陣列用的函式。

[PHP: array_fill - Manual](https://www.php.net/manual/zh/function.array-fill.php)

## array_filter

`array_filter ( array $array [, callable $callback [, int $flag = 0 ]] ) : array`

過濾陣列(自定)函式。

我覺得還滿實用的!!
當 callback 沒有設定，預設 null, ''(空字串)都會被清除
相當實用!!!

> callback
> 使用的回调函数

如果没有提供 callback 函数， 将删除 array 中所有等值为 FALSE 的条目。更多信息见转换为布尔值。

```php
<?php

$arr = ['a' => 1, 'b' => 2, 'c' => 3, 'd' => 4];

var_dump(array_filter($arr, function($k) {
    return $k == 'b';
}, ARRAY_FILTER_USE_KEY));

var_dump(array_filter($arr, function($v, $k) {
    return $k == 'b' || $v == 4;
}, ARRAY_FILTER_USE_BOTH));
?>
// array(1) {
//   ["b"]=>
//   int(2)
// }
// array(2) {
//   ["b"]=>
//   int(2)
//   ["d"]=>
//   int(4)
// }
```

## array_flip

陣列 key 跟 value 做交換

`array_flip ( array $array ) : array`

算是滿實用的函式。

## array_push & \$var[]

`array_push ( array &$array , mixed $value1 [, mixed $... ] ) : int`
回傳 增加數量。

但還是推薦使用`$var[]`

### array_unshift

跟上一個`array_push`很像，但他是加在陣列前面

```php
<?php
$queue = array("orange", "banana");
array_unshift($queue, "apple", "raspberry");
print_r($queue);
?>
// Array
// (
//     [0] => apple
//     [1] => raspberry
//     [2] => orange
//     [3] => banana
// )
```

### array_pop

移除(回傳)後面陣列

### array_shift

移除(回傳)前面陣列

## array_rand

`array_rand ( array $array [, int $num = 1 ] ): mixed`

隨機回傳陣列，第二個陣列可以回傳隨機個數

感覺實用!!

## array_slice

`array_slice ( array $array , int $offset [, int $length = NULL [, bool $preserve_keys = false ]] ) : array`

從陣列中取出一段。

比較特別的是 `$offset`,`$length` 可以用負數，為負數代表就是抓取後面位置
`$offset`,`$length` 可容錯，不會有溢位的問題!!相當時用

**2019-07-01**
今天做這個有 index(key)容錯
假如長度少於 key 內容，會取出陣列最後面一個位置
算是滿好用。
array_unique 後可以省略做 array_values
直接做 array_slice
真的非常方便

array_unique 也可以比對整個 array,object
`sort($arr, SORT_STRING);`
原理如下網站
[PHP 排序模式 SORT_NUMERIC / SORT_STRING / SORT_NATURAL 详解](https://segmentfault.com/a/1190000018487764) {% asset_link web1.png 備份圖 %}

## array_splice

`array_splice ( array &$input , int $offset [, int $length = count($input) [, mixed $replacement = array() ]] ) : array`

## array_sum

把所有陣列做加總。

## array_search

`array_search ( mixed $needle , array $haystack [, bool $strict = FALSE ] ) : mixed`

陣列搜尋，會回傳找到的~~位置~~正確說是 key 值。

### 多維度搜尋

跟 array_search 神搭配

```php
// $userdb=Array
// (
//     (0) => Array
//         (
//             (uid) => '100',
//             (name) => 'Sandra Shush',
//             (url) => 'urlof100'
//         ),

//     (1) => Array
//         (
//             (uid) => '5465',
//             (name) => 'Stefanie Mcmohn',
//             (pic_square) => 'urlof100'
//         ),

//     (2) => Array
//         (
//             (uid) => '40489',
//             (name) => 'Michael',
//             (pic_square) => 'urlof40489'
//         )
// );


$key = array_search(40489, array_column($userdb, 'uid'));
```

### 查詢最後一個 key

```php
array_search('green', array_reverse($array));
```

- [How do I find last occurence of "needle" in array php - Stack Overflow](https://stackoverflow.com/questions/21223286/how-do-i-find-last-occurence-of-needle-in-array-php)

## array_unique

`array_unique ( array $array [, int $sort_flags = SORT_STRING ] ) : array`

> sort_flags
>
> 第二個可選參數 sort_flags 可用於修改排序行為：
>
> 排序類型標記：
> SORT_REGULAR - 按照通常方法比較（不修改類型）
> SORT_NUMERIC - 按照數字形式比較
> SORT_STRING - 按照字符串形式比較
> SORT_LOCALE_STRING - 根據當前的本地化設置，按照字符串比較。

**但我覺得注意 array_unique 移除 value，但是不會合併**
**切記可以需要做 array_value or array_slice**
**切記可以需要做 array_value or array_slice**
**切記可以需要做 array_value or array_slice**

也非一定要用上面兩個
可以做`foreach`
但是做`for($i = 0;$i < count(xxx); $i++)` (PS: $cnt = count(xxx); for($i = 0; $i < $cnt; \$i++>)會比較好)
可能需要注意會有問題!!!

### 類似對二維陣列做 array_column 方法

```php
<?php
$details = array(
    0 => array("id"=>"1", "name"=>"Mike",    "num"=>"9876543210"),
    1 => array("id"=>"2", "name"=>"Carissa", "num"=>"08548596258"),
    2 => array("id"=>"1", "name"=>"Mathew",  "num"=>"784581254"),
);

function unique_multidim_array($array, $key) {
    $temp_array = array();
    $i = 0;
    $key_array = array();

    foreach($array as $val) {
        if (!in_array($val[$key], $key_array)) {
            $key_array[$i] = $val[$key];
            $temp_array[$i] = $val;
        }
        $i++;
    }
    return $temp_array;
}

$details = unique_multidim_array($details,'id');

// $details = array(
//     0 => array("id"=>"1","name"=>"Mike","num"=>"9876543210"),
//     1 => array("id"=>"2","name"=>"Carissa","num"=>"08548596258"),
// );
?>
```

## array_values

我以為有排序合併效果，但是沒有!!

詳細看官往範例

```php
<?php
$a = array(
3 => 11,
1 => 22,
2 => 33,
);
$a[0] = 44;

print_r( array_values( $a ));
// ==>
// Array(
//   [0] => 11
//   [1] => 22
//   [2] => 33
//   [3] => 44
// )
?>
```

## array_walk vs array_map

我目前還很少用到這些，不過這個函式也滿像的，所以特別記錄一下。

簡單說最大的差異 array_walk 會回傳 `true`,`false`，array_map 會回傳 callback 傳回去資料
假如要對原資料變數做更動，使用 array_walk ，**但切記宣告記得在`function callback(&$xxx,...)`**(&符號)

光`&`這點我覺得不是很實用了

詳細：[php array_map 与 array_walk 使用对比 - 偶木 - CSDN 博客](https://blog.csdn.net/u013372487/article/details/51568629)

## in_array

`in_array ( mixed $needle , array $haystack [, bool $strict = FALSE ] ) : bool`

跟 array_search 有點像，但 return 回去是 bollean
詳細可參考：

- [[PHP] in_array 與 array_search 在陣列(Array)中尋找值是否存在 - RicharLin.Tw](https://richarlin.tw/blog/php-in_array-array_search/)

## usort,uksort

自定排序

其實我對 callback 排序記不太住

```php
//由小排到大
$arr = [3,1,2,5,4];
usort($arr,function($a,$b){ return $a > $b}); //大於、負數在前(false)由小排到大
$arr = [3,1,2,5,4];
usort($arr,function($a,$b){
    if($a == $b){
        return 0;
    }
    return $a < $b ? -1 : 1;
});

//由大排到小
$arr = [3,1,2,5,4];
usort($arr,function($a,$b){ return $a < $b}) //小於、正數在前(true)由大排到小
$arr = [3,1,2,5,4];
usort($arr,function($a,$b){
    if($a == $b){
        return 0;
    }
    return $a < $b ? 1 : -1;
});
```

- [PHP usort()的使用 - liuj8929 的博客 - CSDN 博客](https://blog.csdn.net/liuj8929/article/details/79214345)
- [php 陣列排序 usort、uksort 與 sort 函式用法 | 程式前沿](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/222935/)
- [关于 php 中 usort 函数的解读 - 简书](https://www.jianshu.com/p/4d787d88654d)

### PHP 另類比較方法 <=>

#### 太空船操作符（組合比較符）

太空船操作符用於比較兩個表達式。當$a小於(-1)、等於(0)或大於(1)$b 時它分別返回-1、0 或 1。 比較的原則是沿用 PHP 的常規比較規則進行的。

這方法感覺好記!!!
但只能用在 PHP 7.1

```php
<?php // tested on PHP 7.1
$a = [2, 1, 3, 6, 5, 4, 7];
$asc = $desc = $a;
usort($asc, function (int $a, int $b) { return ($a <=> $b); });
usort($desc, function (int $a, int $b) { return -($a <=> $b); });
print_r([ $a, $asc, $desc ]);
```

- [What is <=> (the 'Spaceship' Operator) in PHP 7? - Stack Overflow](https://stackoverflow.com/questions/30365346/what-is-the-spaceship-operator-in-php-7)
- [PHP: rfc:combined-comparison-operator](https://wiki.php.net/rfc/combined-comparison-operator)

還有多個欄位排序方法

```php
function order_func($a, $b) {
    return [$a->x, $a->y, $a->foo] <=> [$b->x, $b->y, $b->foo];
}
// or
function order_func($a, $b) {
    return ($a->$x <=> $b->x) ?: ($a->y <=> $b->y) ?: ($a->foo <=> $b->foo);
}
```

## shuffle

`shuffle ( array &$array ) : bool`

隨機排序陣列函式。

實用!!!

## explode = list()

`explode ( string $delimiter , string $string [, int $limit = PHP_INT_MAX ] ) : array`

```php
$data = "foo:*:1023:1000::/home/foo:/bin/sh";
list($user, $pass, $uid, $gid, $gecos, $home, $shell) = explode(":", $data);
```

老實說我不常這樣寫，算是小記

## array_key_exists() vs isset()

```php

<?php
$search_array = array('first' => null, 'second' => 4);

// returns false
isset($search_array['first']);

// returns true
array_key_exists('first', $search_array);
?>

```

## 其他小記(感想)

最近寫久了，雖然覺得 PHP 處理 array 很方便，但發現都是 function base 程式寫上面不是很方便
最近剛好看到 Laravel 有 Collection ， 改善我上面說的缺點
有爬過文章說跟 array 校能差不多!!
[集合 - Laravel - 為網頁藝術家創造的 PHP 框架](https://laravel.tw/docs/5.2/collections)
