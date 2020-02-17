---
title: Redis 小記
date: 2020-02-17 16:45:25
tags: [redis]
categories: Redis
---

常常專案使用 redis
但並沒有深入了解
因為 Redis 一般看範例就知道怎麼使用了
真的非常簡單
但我覺得用那麼多次
沒深入了解不是很好
這篇整理一下

<!--more-->

## 安裝

上一篇是用 ansible 安裝 redis
這篇就不做了...

其個更快的話可以用 `apt install redis-server`

## Redis 一般常用類型

1. String (最大可存 512 MB)
2. Hash
3. List
4. Map
5. Sorted Map(zmap)

## 一般常用指令

### keys *

一般線上不會用這個指令，好像會有阻塞的可能。

### dbsize 

回傳 `key` 的數量。

### exists

查詢 `key` 是否存在。

return 1 存在，0 不存在

### del

刪除 key

### expire

設定 key 結束時間

### ttl

查看 key 剩餘時間

-1 沒有限制
-2 沒有 key 存在
### type

查看 key 的類型

### incr

會把 key 的 value 加 1

這個是 redis 適合做計數器

## 類型相關指令

### String

#### set

set 

四個 option

#### setex

setex key second value

跟 
set key value
expire key second 
一樣動作

set key value ex second

#### setnx

key 不存在才做設定值

#### mset

mset key1 value1 key2 value2

可以多個設定值

一般 set 
n次set時間=n次網路時間+n次命令
但是 mset 只需要一次網路時間
mset時間=`1`次網路時間+n次命令

#### mget

跟 mset 與 set 關係差不多
如上。

找不到會回傳 nil(null)

#### append

append key value

會在 value 值後面加入新 value 值
就算不存在也不會有問題

#### strlen

key 的 value 長度
沒資料的話，回傳 0

#### getset

getset key value
回傳值是舊值

### Hash

#### hset

hset key field value

hset user:1 name josh

#### hget

hget key field

hget user:1 name

#### hsetnx

hsetnx key field value

#### hdel

hdel 刪除 key 的 field

#### hlen

hlen 抓取 key 的長度

#### hkeys

這邊 hkeys 跟 key 完全沒有關係
列出 key 裡面的 field

#### hmset,hmget

hmset key field1 value1 field2 value2 ...

#### hexist

hmget key field1 filed2 ...

#### hvals

hvals key 

#### hgetall

hgetall 會把所有field,value抓出來。
老實說我還不知道這個能做什麼?
會阻塞，可以使用 hscan 代替

#### hstrlen

抓取 key field 長度

#### hincrby

hincrby key field 1
回傳累加值

#### hincrbyfloat

hincrbyfloat key filed 
回傳累加值

### List

#### lpush

lpush  key value

l => left 
在 list 左邊加入 value

#### rpush

r => right
同上，但家在右邊 list

#### lpop

l => left 
移除(拿出) 左邊 list 的 value

#### rpop

同上，是右邊

#### llen

llen list(key)

#### lrem

lrem list(key) count value 

> 根據參數 count 的值，移除列表中與參數 value 相等的元素。
> count 的值可以是以下幾種：
>     count > 0 : 從表頭開始向表尾搜索，移除與 value 相等的元素，數量為 count 。
>     count < 0 : 從表尾開始向表頭搜索，移除與 value 相等的元素，數量為 count 的絕對值。
>     count = 0 : 移除表中所有與 value 相等的值。


[LREM key count value — Redis 命令参考](http://redisdoc.com/list/lrem.html)

#### lrange


> 返回列表 key 中指定區間內的元素，區間以偏移量 start 和 stop 指定。
> 
> 下標(index)參數 start 和 stop 都以 0 為底，也就是說，以 0 表示列表的第一個元素，以 1 表示列表的第二個元素，以此類推。
> 
> 你也可以使用負數下標，以 -1 表示列表的最後一個元素， -2 表示列表的倒數第二個元素，以此類推。

[LRANGE key start stop — Redis 命令參考](http://redisdoc.com/list/lrange.html)

#### lindex

lindex key index
回傳 list 的位置 value值

#### brpop

阻塞 rpop

#### brpush

阻塞 rpush

#### 總整理

新增: rpush lpush linsert
查詢: lrange lindex llen
刪除: lpop rpop lrem ltrim
修改: lset
阻塞動作: blpop brpop

Message Queue 消息對列 是用 lpush,brpop
但跟 lpush ,rpop 不能做嗎?
後來發現 rpop 不會阻塞
在這邊程式用 while 迴圈 CPU 一定會爆表(吃很多資源)

###  Set

#### sset

sset key element ...

#### srem 

srem key element 

#### scard

算出 set 個數

#### sismember

set is member ?

判斷 set 裡面有沒有 指定的值

#### spop

移除一個 set 的值

回傳移除的值

#### srandmember

srandmember key [count]

隨機回傳 set 的 member(資料)

#### smember

smember key

回傳 set 所有值(member)

PS: smembers 和 lrange,hgetall 都會造成阻塞可能性。通常會用 sscan 替代

#### set 運算集

##### sinter (交集)

sinter key [key ...]

##### sunion (聯集)

sunion key [key ...]

##### sdiff (差集)

sdiff key [key ...]

##### 集合存在...

sinterstore destination key [key ...]
sunionstore destination key [key ...]
sdiffstore destination key [key ...]

### sorted set

#### zadd

sadd key [option] score member [score member ...]

**option**
nx: member 必須不存在
xx: member 必須存在
CH：修改從加入新的元素，以改變元件的總數（CH是改變的縮寫）的數目的返回值. 更改的元素是添加的新元素以及已為其更新分數的現有元素. 因此，命令行中指定的具有與過去相同分數的元素將不計算在內. 注意：通常， ZADD的返回值僅計算添加的新元素的數量.**這個還不是很懂**
[ZADD – Redis 中文文檔教程](https://s0redis0io.icopy.site/commands/zadd)
INCR: 數值累加

#### zcard 

查詢 zset 個數

#### zsocre

這個很重要。沒有 zget
zscore key member
得到設定分數


#### 排名
##### zrank 

zrank key member

查詢 member 排名
由小到大

##### zrevrank

zrevrank 排名(倒敘)
由大到小

#### zrem

zrem key member [member ...]

刪除 member

#### zincrby 

zincrby key increment member

給 member 增加 score

#### zrange,zrevrange

由小到大
zrange key start end [withscores]
由大到小
zrevrange key start end [withscores]

ex: zrange zset 0 100

withscores 是同時返回 score 分數

#### zrangebyscore, zrevrangebyscore

zrangebyscore key min max [withscores] [limit offset count]

zrevrangebyscore key min max [withscores] [limit offset count]

指定範圍返回 member

-inf,+inf => 無限小,無限大
小括號 `(` 大於
ex: zrangebyscore user:rank (200 +inf withscores

#### zcount

zcount key min max 

抓取分數區間的 member 個數

#### zremrangebyrank , zremrangebyscore

zremrangebyrank key start end

刪除 rank 排名(0開始)

ex: zremrangebyrank user:rank 0 3

zremrangebyscore key min max

zremrangebyscore user:rank (250 +inf

#### 集運算

交集
zinterstore destination numkeys key [key ...] [weights weight [weight ...]] [aggregate sum|min|max]

weight 權重
aggregate 交集後做的 score 動作。預設是 sum

并集

zunionstore destination numkeys key [key ...] [weights weight [weight ...]] [aggregate sum|min|max]


#### zset 小結論

set 跟 zset 指令差非常多
一般常用在 排行榜上面


##### 有在思考資料會一直存在 redis ?

經過 Google 一翻，正常會把資料回傳到 MySQL
- [PHP+Redis 实例【一】点赞 + 热度 上篇 - H-大叔 - 博客园](https://www.cnblogs.com/sunshine-H/p/7922285.html)
- [PHP+Redis 实例【一】点赞 + 热度 下篇 - H-大叔 - 博客园](https://www.cnblogs.com/sunshine-H/p/7928034.html)

更多文章
- [使用MySQL、Redis解决排行榜问题 - 簡書](https://www.jianshu.com/p/8060708a5b46)
- [千万用户排名设计 - 知乎](https://zhuanlan.zhihu.com/p/50770947)
- [云风的 BLOG: 谈谈陌陌争霸在数据库方面踩过的坑(排行榜篇)](https://blog.codingnow.com/2014/03/mmzb_db_2.html)

## 搜尋一些

### redis 學習路線

網路上找到的圖

{% asset_img redis.png Redis學習路線 %}

### 一些找網路文章

- [Redis 应用场景汇总 | LearnKu 终身编程者的知识社区](https://learnku.com/articles/33421)
- [php - redis使用list做消息队列,为什么推送用lpush,而消费用的是brpop? - SegmentFault 思否](https://segmentfault.com/q/1010000010777585)
- [redis专题16 数据持久化实践 | 一线攻城狮](https://researchlab.github.io/2018/10/13/redis-16-storage-solution-opt/)
- [Redis SCAN的使用 - 静中细思的博客 | Kingnet Blog](http://jinguoxing.github.io/redis/2018/09/04/redis-scan/)
- [阿里云Redis开发规范-云栖社区-阿里云](https://yq.aliyun.com/articles/531067)
- [Jedis常见异常汇总-云栖社区-阿里云](https://yq.aliyun.com/articles/236384?spm=a2c4e.11153940.0.0.fdaf2d39jbvGgk)