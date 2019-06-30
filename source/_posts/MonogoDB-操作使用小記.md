---
title: MonogoDB 操作使用小記
date: 2019-06-30 13:13:03
tags: [monogodb]
categories: MongoDB
---

最近專案有用到這個
有看到使用`Robo 3T`
但對這一塊不是很了解
這邊主要是紀錄如何`操作 MonogoDB`
不會記載 MonogoDB 詳細知識

<!--more-->

## 有哪幾種 NoSQL?(4 種)

- Key-value store
  ex: BigTable、Hadoop
- Document store
  ex: MongoDB
- Graph
  ex: Neo4J
- 列存儲(Wide Column Store/Column-Family)資料庫(記憶體資料庫是知名網站慣用快取工具)
  ex: Memcached、Redis、Velocity

[快速認識 4 類主流 NoSQL 資料庫 | iThome](https://www.ithome.com.tw/news/92507)

## 簡單架設 MonogoDB

### docker 架設

[參考裡面 docker-compose.yml](https://hub.docker.com/_/mongo)

### vagrant

homestead 可以架設

或者比較單純[Vagrant box benson/mongodb - Vagrant Cloud](https://app.vagrantup.com/benson/boxes/mongodb) 或 [Update to Mongo v3 · Issue #5 · bobthecow/vagrant-mongobox](https://github.com/bobthecow/vagrant-mongobox/issues/5)

## 操作使用

### local,config,admin databases

預設安裝完會看到有三個 database
`local`,`config`,`admin` 三個 database 建議不要去動他

> admin and local contain various settings local to the server, like users who are authenticated to connect. Under beginner usage, you shouldn't need to worry about them at all. By default you connect to a database named test. To connect to a new database, just use databasename from the mongo command line, or mongo databasename from your OS shell.
> use [database_name] and then show collections
> The db object is your root handle to the currently-selected database on the mongo commmand line. The command line is really just a Javascript command line, and there are various mongodb-specific objects and functions exposed that let you do stuff. Try help() for a full listing.

[Some beginner's questions about MongoDB - Stack Overflow](https://stackoverflow.com/questions/3490272/some-beginners-questions-about-mongodb)
[MongoDB 管理：慎用 local、admin 数据库 | MongoDB 中文社区](http://www.mongoing.com/archives/2599)

## mysql <=> mongodb

MySQL MongoDB
Table <==> Collection
Row <=> Document
Column <=> Field

[MongoDB and MySQL Compared | MongoDB](https://www.mongodb.com/compare/mongodb-mysql)

## 操作使用

### 創建資料庫

`use DATABASE_NAME`

如果資料庫不存在，則創建資料庫，否則切換到指定資料庫。

### 刪除資料庫

`db.dropDatabase()`

刪除所在的 Database

### 创建集合(Collection)

`db.createCollection(name, options)`

`db.createCollection("mycol", { capped : true, autoIndexId : true, size : 6142800, max : 10000 }`
創建固定集合 mycol，整個集合空間大小 6142800 KB, 文檔最大個數為 10000 個。

### 插入文件(Document)

`db.COLLECTION_NAME.insert(document)`

```js
db.col.insert({
  title: "MongoDB 教程",
  description: "MongoDB 是一個 Nosql 數據庫",
  by: "菜鳥教程",
  url: "http://www.runoob.com",
  tags: ["mongodb", "database", "NoSQL"],
  likes: 100
});
```

### 查詢集合

`db.col.find()`

### 刪除文件

```js
db.collection.remove(
   <query>,
   <justOne>
)
```

[MongoDB 刪除文檔 | 菜鳥教程](http://www.runoob.com/mongodb/mongodb-remove.html)

```
remove() 方法已經過時了，現在官方推薦使用 deleteOne() 和 deleteMany() 方法。

如刪除集合下全部文檔：

db.inventory.deleteMany({})

刪除 status 等於 A 的全部文檔：

db.inventory.deleteMany({ status : "A" })

刪除 status 等於 D 的一個文檔：

db.inventory.deleteOne( { status: "D" } )

三國電視台

   三國電視台

  tre***3@126.com
2年前 (2017-09-22)

   sairre

  jsa***e@163.com

remove() 方法 並不會真正釋放空間。

需要繼續執行 db.repairDatabase() 來回收磁盤空間。

> db.repairDatabase()
或者
> db.runCommand({ repairDatabase: 1 })
```

### 修改文件

```js
db.collection.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
)
```

```
在3.2版本开始，MongoDB提供以下更新集合文档的方法：

    db.collection.updateOne() 向指定集合更新单个文档
    db.collection.updateMany() 向指定集合更新多个文档
```

更多內容可看[MongoDB 教程 | 菜鸟教程](http://www.runoob.com/mongodb/mongodb-tutorial.html)
由於裡面教學應該是 2.x 版
可以看評論裡面補充很多東西

## Robo 3T 操作 MongoDB

[MongoDB 可视化工具--Robo 3T 使用教程 - 龙恩 0707 - 博客园](https://www.cnblogs.com/tugenhua0707/p/9250673.html)
[adminMongo](https://adminmongo.markmoffat.com/)

## 刪除資料 OR 篩選資料 的問題

[「乾貨」mongoDB 釋放磁碟占用 - 每日頭條](https://kknews.cc/zh-tw/news/kyyva3q.html)
目前專案裡面放的資料好像不會去做刪除或者撈出來資料不會做篩選
不知道是不是非常耗效能關係??
