---
title: 'MySQL在Linux下預設有分大小寫和Incorrect integer value: '''' for column ''num'' 問題'
date: 2018-08-28 21:48:51
tags: [MySQL]
categories: 寫程式一些事
---

其實對MySQL管理那一塊真的不是很熟
但最近踩到一堆雷
還是記一下 Linux跟Windows運行MySQL會不一樣的雷
哪天說不定還會踩到orz

<!--more-->

## MySQL在Linux下預設有分大小寫
之前在玩Docker發現MySQL底下，Linux有分大小寫
奇怪，為什麼我大學在Windows下就沒事
當初在轉docker時候遇到這個問題
當下就只記window大小寫沒分，Linux有分大小寫
但沒有理解為什麼
不過，今天有爬到[MySQL表名大小写敏感导致的问题 - CSDN博客](https://blog.csdn.net/postnull/article/details/72455768)
原來用window同一個目錄不能存在`test.txt`和`Test.txt`這兩個檔案(我用window20多年都不知道有這個問題 囧)

### 查看是否有用大小寫變數
```
show variables like '%lower_case_table_names%';
```
### 修改大小寫方法
> 修改变量lower-case-table-names
>
>为什么会出现这个问题，主要是因为开发和测试环境不一致导致的，这也是有时测试环境完全没问题，一到生产就出诡异问题的原因之一。所以，我们除了让测试同事认真测试外，最好本地和测试环境还是一致。
对当前这个问题，我们直接修改SQL中的Emp为emp即可解决问题，但是为了根本解决问题，我们还须修改mysql的环境变量，也就是将lower_case_table_names的值改为0.
修改方法：
1. 找到mysql的安装位置
2. 找到主目录下的my.ini配置文件
3. 在[mysqld]节点下添加lower-case-table-names=0
4. 重启mysql服务
來源[MySQL表名大小写敏感导致的问题 - CSDN博客](https://blog.csdn.net/postnull/article/details/72455768)


## Incorrect integer value: '' for column 'id'
最近事情這樣的，公司有程式做報表匯入動作`Incorrect integer value: '' for column 'id'`
經過查詢SQL語法發現，目前程式匯入某欄位剛好抓到price為空
導致SQL會有問題

當下我以為應該不能跑
不過在我開發環境(window)竟然可以跑
```sql
INSERT INTO  `test`(test ,num)
 values ( 'fff','');
```
注意`num`為int
開發環境竟然可以跑!!!!!

更奇葩的是`fff`也能過
{% asset_img mysql_warning.jpg MySQL_Warning %}

後來有找到[解决Incorrect integer value: '' for column 'id' at row 1的方法 - CSDN博客](https://blog.csdn.net/testcs_dn/article/details/39670421)
是sql_mode有開啟`STRICT_TRANS_TABLES`

>my.ini中查找sql-mode，默认为sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION"，
将其修改为sql-mode="NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION"，重启mysql后即可
參考來源:
* [解决Incorrect integer value: '' for column 'id' at row 1的方法 - CSDN博客](https://blog.csdn.net/testcs_dn/article/details/39670421)
* [STRICT_TRANS_TABLES(存储引擎启用严格模式，非法数据值被拒绝)。 - Jesson - ITeye博客](http://hello-jesson.iteye.com/blog/1569388)


## 結論
用MySQL開發還是用Docker開發，不然MySQL在Window和Linux兩個環境真的差好多  囧
不然事後上線真的很慘，至於公司為什麼以前可以跑
好像上禮拜DB掛掉重裝，那台MySQL裡面sql_mode變數有設定STRICT_TRANS_TABLES
所以導致以前可以跑，但現在不能

沒事還是不要用Windows 囧


```
select @@sql_mode
-- window底下(xxamp)
-- NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
-- 公司底下(別台設定)
-- NO_ENGINE_SUBSTITUTION
-- docker 啟動mysql(不知道為什麼設定一大堆......)
-- ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
```

