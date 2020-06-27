---
title: MySQL sharding初步認識小記
date: 2019-05-03 22:47:18
tags: [sharding, mysql]
categories: MySQL
---

之前有做 Sharding 簡單紀錄[MySQL 不錯優化文章&DB 雜記 | 程式狂想筆記](https://malagege.github.io/blog/2018/12/27/MySQL%E4%B8%8D%E9%8C%AF%E5%84%AA%E5%8C%96%E6%96%87%E7%AB%A0-DB%E9%9B%9C%E8%A8%98/)
最近工作上發現我還是沒有很了解 Sharding
在工作上有很大的誤解
當然這篇不會記錄到很深入
只是記錄我的錯誤觀念改正

<!--more-->

## 首先做 Sharding，不能做 join 動作

因為先前開會，對方沒有講很清楚
我以為部分資料表可以同步到別的 Database
想說還是能做 join 問題
(沒想到沒有適用 mariadb sharding 功能)
但是，最近問相關問題的時候
問到 join 相關事情，有得到消息是`不能做join`事情
當然也不會使用到類似 MariaDB Spider 東西
連線所有抓資料`要用程式處理`
當然有些 count,group by 動作可能都用程式處理了

~~當然想說這麼複雜的東西交給工程師用很冏~~

> 3、跨節點合併排序分頁問題
> 一旦進行了數據的水平切分之後，可能就並不僅僅只有跨節點 Join 無法正常運行，有些排序分頁的 Query 語句的數據源可能也會被切分到多個節點，這樣造成的直接後果就是這些排序分頁 Query 無法繼續正常運行。其實這和跨節點 Join 是一個道理，數據源存在於多個節點上，要通過一個 Query 來解決，就和跨節點 Join 是一樣的操作。同樣 Federated 也可以部分解決，當然存在的風險也一樣。
> 還是同樣的問題，怎麼辦？我同樣仍然繼續建議通過應用程序來解決。
> 如何解決？解決的思路大體上和跨節點 Join 的解決類似，但是有一點和跨節點 Join 不太一樣，Join 很多時候都有一個驅動與被驅動的關係，所以 Join 本身涉及到的多個表之間的數據讀取一般都會存在一個順序關係。但是排序分頁就不太一樣了，排序分頁的數據源基本上可以說是一個表（或者一個結果集），本身並不存在一個順序關係，所以在從多個數據源取數據的過程是完全可以並行的。這樣，排序分頁數據的取數效率我們可以做的比跨庫 Join 更高，所以帶來的性能損失相對的要更小，在有些情況下可能比在原來未進行數據切分的數據庫中效率更高了。當然，不論是跨節點 Join 還是跨節點排序分頁，都會使我們的應用服務器消耗更多的資源，尤其是內存資源，因為我們在讀取訪問以及合併結果集的這個過程需要比原來處理更多的數據。
> 分析到這裡，可能很多讀者朋友會發現，上面所有的這些問題，我給出的建議基本上都是通過應用程序來解決。大家可能心裡開始犯嘀咕了，是不是因為我是 DBA，所以就很多事情都扔給應用架構師和開發人員了？
> 其實完全不是這樣，首先應用程序由於其特殊性，可以非常容易做到很好的擴展性，但是數據庫就不一樣，必須借助很多其他的方式才能做到擴展，而且在這個擴展過程中，很難避免帶來有些原來在集中式數據庫中可以解決但被切分開成一個數據庫集群之後就成為一個難題的情況。要想讓系統整體得到最大限度的擴展，我們只能讓應用程序做更多的事情，來解決數據庫集群無法較好解決的問題。
> 作者：浮浮尘尘
> 链接：https://www.jianshu.com/p/6ebafabfddae
> 来源：简书
> 简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

~~看著看著眼淚掉出來~~

- [数据库 Sharding 的基本思想和切分策略 - Laurence 的技术博客 - CSDN 博客](https://blog.csdn.net/bluishglc/article/details/6161475)
- [MySQL Sharding 详解 - 简书](https://www.jianshu.com/p/6ebafabfddae)
- [MySQL 分库分表，写得太好了！ - 51CTO.COM](http://database.51cto.com/art/201809/583857.htm)
- [MySQL 上 sharding 的方案 | Gea-Suan Lin's BLOG](https://blog.gslin.org/archives/2017/02/02/7113/mysql-%E4%B8%8A-sharding-%E7%9A%84%E6%96%B9%E6%A1%88/)
- [MySQL 不錯優化文章&DB 雜記 | 程式狂想筆記](https://malagege.github.io/blog/2018/12/27/MySQL%E4%B8%8D%E9%8C%AF%E5%84%AA%E5%8C%96%E6%96%87%E7%AB%A0-DB%E9%9B%9C%E8%A8%98/)
- [数据库 Sharding 的基本思想和切分策略（分库分表） | 忆桐之家的博客](http://hongyitong.github.io/2016/12/16/%E5%88%86%E8%A1%A8%E5%88%86%E5%BA%93%EF%BC%88%E6%95%B0%E6%8D%AE%E5%BA%93%E6%80%A7%E8%83%BD%E8%B0%83%E4%BC%98%EF%BC%89%20/)
- [方案虽好，成本先行：数据库 Sharding+Proxy 实践解析 - MySQL - dbaplus 社群：围绕 Data、Blockchain、AiOps 的企业级专业社群。技术大咖、原创干货，每天精品原创文章推送，每周线上技术分享，每月线下技术沙龙。](https://dbaplus.cn/news-11-1854-1.html)
- [MySql 從一竅不通到入門（五）Sharding：分表、分庫、分片和分割槽 - IT 閱讀](https://www.itread01.com/content/1545726433.html)
- [MySql 从一窍不通到入门（九）Sharding 的实现和规避 - Invoker's Tower - CSDN 博客](https://blog.csdn.net/KingCat666/article/details/78325158)
- [现在 mysql 的分布式数据访问层主流方案有哪些? - 知乎](https://www.zhihu.com/question/22521550)
- [传统分库分表(sharding)的缺陷与破解之法-云栖社区-阿里云](https://yq.aliyun.com/articles/404971)

## 分表方式

1. 用非 Sharding 某一張記錄 sharding，如[MySQL 分库分表，写得太好了！ - 51CTO.COM](http://database.51cto.com/art/201809/583857.htm)

用程式去做導向動作

2. 用%(程式餘數)去做分割

當初看這段有想到之後擴增 Sharding 不是要做資料轉移
剛好這篇有提到 [数据库 sharding 和一致性哈希算法 - 言川的博客 - CSDN 博客](https://blog.csdn.net/lihongxun945/article/details/51482903)
擴增事要做資料轉移的

現有 code，可以參考 open bilibili 有實作這個功能 XDD

## Sharding 各 table 的主鍵 id 不能重複

1.  Sharding DB 設定 auto_increment 起始不一樣。(ex:DB1 0, DB2 10000000)
2.  拿不會重複資料串流水號，(EX:(USER_ID)UUID+id)

## 另類 DB 實作作法

> Vitess 就是由 Youtube 團隊為了解決資料量龐大、邏輯複雜導致 Query 的錯誤率升高，讓 Youtube 工程團隊有解不完的 Bug、加不完的班。又幸好當時寫這套前端窗口的人致力開源這份程式碼，才讓後世的人能不用辛苦地寫一份這種可怕的東西(我想也沒多少人寫得出來)。

[对比 Vitess，ShardingSphere 有哪些不同](https://www.infoq.cn/article/NHSAAmN*MfpLiTiTTEu5)
[Mysql --- sharding , partitioning | Richard's Blog](https://a28283878.github.io/documents/Database-Scaling-Vitess.html)
kingshard
[kingshard/how_to_use_kingshard.md at master · flike/kingshard · GitHub](https://github.com/flike/kingshard/blob/master/doc/KingDoc/how_to_use_kingshard.md)

## 我有個大膽的想法

如果兩個 Array 做 join 動作有什麼做法?

後來有找到這個
[LINQ PHP 比較：YaLinqo，Ginq，Pinq*Web 開發*幫酷](https://hant.helplib.com/Web_Development/article_3813)
[YaLinqo port de .NET 4 LINQ vers PHP - Par l'exemple](http://io.gchatelier.fr/blog/yalinqo-linq-php-exemple/#joio)

**Yalinqo 裡面的 join 跟 SQL Join 好像不太一樣**
當然也有找到程式自幹的，[How to simulate the SQL LEFT JOIN operation using PHP arrays? - Stack Overflow](https://stackoverflow.com/questions/25836473/how-to-simulate-the-sql-left-join-operation-using-php-arrays)

```js
//function to simulate the left join
    function left_join_array($left, $right, $left_join_on, $right_join_on = NULL){
        $final= array();

        if(empty($right_join_on))
            $right_join_on = $left_join_on;

        foreach($left AS $k => $v){
            $final[$k] = $v;
            foreach($right AS $kk => $vv){
                if($v[$left_join_on] == $vv[$right_join_on]){
                    foreach($vv AS $key => $val)
                        $final[$k][$key] = $val;
                } else {
                    foreach($vv AS $key => $val)
                        $final[$k][$key] = NULL;
                }
            }
        }
       return $final;
    }
```

比較特別要 join 兩個欄位，名稱可以不一樣，所以後面兩個參數是用來對應關聯屬性
我這邊有簡單實作[PHP Sandbox, test PHP online, PHP tester](http://sandbox.onlinephpfunctions.com/code/72c7b55bc7cf39f57454c5641e9e1ffd4758b178)

後來發現 join 空資料時候會有問題，小小修正

```js
    function left_join_array($left, $right, $left_join_on, $right_join_on = NULL){
        $final= array();

        if(empty($right_join_on))
            $right_join_on = $left_join_on;

        foreach($left AS $k => $v){
            $final[$k] = $v;
            foreach($right AS $kk => $vv){
                if($v[$left_join_on] == $vv[$right_join_on]){
                    foreach($vv AS $key => $val)
                        $final[$k][$key] = $val;
                } else {
                    foreach($vv AS $key => $val){
                        if($key == $left_join_on) continue;
                        $final[$k][$key] = NULL;
                    }

                }
            }
        }
       return $final;
    }
```

改寫 inner join

```js
    function left_join_array($left, $right, $left_join_on, $right_join_on = NULL){
        $final= array();

        if(empty($right_join_on))
            $right_join_on = $left_join_on;

        foreach($left AS $k => $v){
            foreach($right AS $kk => $vv){
                if($v[$left_join_on] == $vv[$right_join_on]){
                    $final[$k] = $v;
                    foreach($vv AS $key => $val)
                        $final[$k][$key] = $val;
                }
            }
        }
       return $final;
    }
```

### 找到其他套件

[GitHub - erdalceylan/array-join: php array join](https://github.com/erdalceylan/array-join)

### 排序問題

[php - How to Sort Multi-dimensional Array by Value? - Stack Overflow](https://stackoverflow.com/questions/2699086/how-to-sort-multi-dimensional-array-by-value)

### 效能上的問題

> 其次你 left_join_array 功能是一個問題，它的速度是 O(n^3)，速度很慢，並儘可能內存去你分配噸也是如此，但在我們談論如何優化數據之前，我需要更多關於您想要處理數據的信息。
> MySQL 將總是比任何你能在幾分鐘內寫速度更快，尤其是如果你沒有關於如何 RDBMS 工作的任何知識。您可以儘可能多地模擬它，但 MySQL 應該至少快一個數量級; MySQL 的連接算法是 O((n*m) log(n*m))，你的算法指數級地慢（字面上）。

[PHP 脚本 - VoidCC](http://cn.voidcc.com/question/p-vznlcvcp-tn.html)
[algorithm - PHP script for optimizing loading CSV data - Stack Overflow](https://stackoverflow.com/questions/46408886/php-script-for-optimizing-loading-csv-data/46409077#46409077)
請用在資料少上面

### 物件快速轉陣列方法

`json_decode(json_encode(\$products), true);`
[Convert PHP object to associative array - Stack Overflow](https://stackoverflow.com/questions/4345554/convert-php-object-to-associative-array)

其他想到再補...


**2020-06-13**

最近在看 Procedure stored 處理大量資料是不是最好解
看起來大量資料是...
在這之間有查到 sharding 文章覺得不錯，並整理一下

[寫好SQL很有必要！ - 數據分析那些事 - Medium](https://medium.com/@allaboutdataanalysis/%E5%AF%AB%E5%A5%BDsql%E5%BE%88%E6%9C%89%E5%BF%85%E8%A6%81-3206014d5422) {% asset_link web1.png 備份圖 %}

[分庫分表的基本思想 - -j神----- - 博客園](https://www.cnblogs.com/jshen/p/7682502.html) {% asset_link web2.png 備份圖 %}


其他沒相關的整理

[使用非同步處理提升資料庫更新速度-黑暗執行緒](https://blog.darkthread.net/blog/async-batch-update-performance/)

[oracle - Can I rollback after calling stored procedure in java test? - Stack Overflow](https://stackoverflow.com/questions/46051642/can-i-rollback-after-calling-stored-procedure-in-java-test)

**2020-06-18**

- [一次分表踩坑实践的探讨 - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/article/1506228)
sharding 做報表還是很大的難題阿!!

- [SpringBoot2.x集成Sharding-JDBC实现分库分表 - 简书](https://www.jianshu.com/p/9a21b4f9c3dd)

- [Spring boot+mybatis+mysql+ sharding-jdbc实现分库分表学习demo - 簡書](https://www.jianshu.com/p/74c02a2a89de)

- [為什麼要用spring-data-jpa而不直接用hibernate | 程式前沿](https://codertw.com/ios/61625/)