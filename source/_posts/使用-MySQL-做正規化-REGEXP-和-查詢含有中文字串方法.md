---
title: 使用 MySQL 做正規化 (REGEXP) 和 查詢含有中文字串方法
date: 2020-02-20 22:04:59
tags: [mysql, regex]
categories: SQL
---

最近報表需要撈出中文字
找了一些方法可以用 SELECT 句子撈出

這篇尚未整理，有空再測試
<!--more-->


## UTF-8 中文範圍

[utf-8 繁體中文編碼表 範圍 是多少_百度知道](https://zhidao.baidu.com/question/480799001.html)
[MySQL 判断中文字符 | Cryptospace](https://dbarobin.com/2015/01/16/distinguish-chinese-characters-in-mysql/)
[utf-8,Unicode (UTF-8),utf-8编码,Unicode (UTF-8)编码,utf-8字符,Unicode (UTF-8)字符,utf-8编码对照表,Unicode (UTF-8)编码对照表](https://www.haomeili.net/CodeDown/html/double/all/utf-8_65001.html)
[整理 Unicode 經常會使用到的內碼區域並透過 Regex 自動比對文字 | The Will Will Web](https://blog.miniasp.com/post/2019/01/02/Common-Regex-patterns-for-Unicode-characters)


## SQL 方法

### 使用正規化

mysql> SELECT v
    -> FROM
    -> (
    ->    SELECT 'sql server是微软开发的数据库管理系统'  AS v UNION ALL
    ->    SELECT 'C-123456789' AS v UNION ALL
    ->    SELECT 'MySQL是一个开源的数据库管理系统' AS v UNION ALL
    ->    SELECT 'sdalfkj'
    -> )t
    -> WHERE v REGEXP '[吖-座]'=1;
+--------------------------------------+
| v                                    |
+--------------------------------------+
| sql server是微软开发的数据库管理系统 |
| MySQL是一个开源的数据库管理系统      |
+--------------------------------------+
2 rows in set (0.03 sec)
————————————————
版权声明：本文为CSDN博主「不想长大啊」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/sqlserverdiscovery/article/details/50443042

### length和char_length

```sql
SELECT length("你好"),char_length("你好");
```
長度不一樣就是含有非英文字!!


## 其他小記

[【MySQL编程你也行】MySQL 识别是否是中文字符的方法_数据库_SQL Server探索-CSDN博客](https://blog.csdn.net/sqlserverdiscovery/article/details/50443042)

[» MySQL 两种识别是否有中文字符的方法](http://www.zhdba.com/mysqlops/2011/02/21/mysql-%E4%B8%A4%E7%A7%8D%E8%AF%86%E5%88%AB%E6%98%AF%E5%90%A6%E6%9C%89%E4%B8%AD%E6%96%87%E5%AD%97%E7%AC%A6%E7%9A%84%E6%96%B9%E6%B3%95/)

[Unicode 编码相关 - 简书](https://www.jianshu.com/p/81bfe3db7bcf)


```SELECT 'ALL I NEED IS YOU の' REGEXP '^[[:<:]]ALL I NEED IS YOU[[:>:]] *((-.+)|([\[].+[\]])|([【].+[】])|([\(].+[\)])|([一-龥]+))$'```


[oracle查詢欄位是否含有中文 - IT閱讀](https://www.itread01.com/content/1543457232.html)