---
title: MySQL 使用變數和 vscode 快速產生內容範本
date: 2019-09-15 20:21:53
tags: [mysql]
categories: MySQL
---

最近重複的工作做一些改進
如每個月重複執行 SQL 內容
需要更快、還要更方便設定方法

<!--more-->

## MySQL set variable

```sql
set @_id = xxx;
-- SELECT MAX(id)+1 FROM `xxx`
set @name = 'name';
-- SELECT * FROM `xxx` WHERE name LIKE '%name%'

set @money = -140; -- how much
set @end = NULL; -- format(EX:2018-10-26 19:00:00) or NULL
set @bye_date = NULL; -- format(EX:2018-10-26 19:00:00) or NULL



INSERT INTO `pay_type`(id,name,money)  VALUES(@_id,@name,@money);

...

```

[MySQL :: MySQL 8.0 Reference Manual :: 13.7.6.1 SET Syntax for Variable Assignment](https://dev.mysql.com/doc/refman/8.0/en/set-variable.html)

有用過，不能用在 `CREATE TABLE ...`


## vscode Blueprint

不管任何程式都可以使用
詳細看裡面的測試圖片就可以了解怎麼使用
[reesemclean/blueprint: Create files and folders of files from templates in Visual Studio Code](https://github.com/reesemclean/blueprint)

./blueprint-templates/測試demo/__name__test.sql
```sql
CREATE TABLE `xxxx`.`{{ $table }}` (
  `id` int(11) NOT NULL auto_increment,
  `s` varchar(10) NOT NULL,
  `s2` varchar(10) NOT NULL,
  `user_number` varchar(100) NOT NULL,
  PRIMARY KEY  (`id`)
  KEY `s` (`s`),
  KEY `s2` (`s2`)
) ENGINE=InnoDB  DEFAULT CHARSET=utf8 COMMENT='{{$表格註解}}' AUTO_INCREMENT=1 ;
```


## 其他小記


有一個 Blueprint 跟 API Blueprint 沒有關係
相關教學連結
[Kewang 的資訊進化論 - 貼文](https://www.facebook.com/kewang.information/posts/1940647046211660)

