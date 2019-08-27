---
title: MySQL Partition 小記
date: 2019-06-25 22:20:58
tags: [mysql, partition]
categories: MySQL
---

最近接觸一點 Partition
但這個東西不是很熟悉
所以小記一些東西(都是網路上找的)

<!--more-->

## 主鍵和 Parition

`1503 - A PRIMARY KEY must include all columns in the table's partitioning function`
[MYSQL 的分区字段，必须包含在主键字段内 - 王 庆 - 博客园](https://www.cnblogs.com/zhishan/p/3285055.html)


**2019-08-26 小記**
要把 patition 的欄位加到 PRIMARY KEY

> 簡單說說我的看法
> **partition 用到的欄位要加進去 primary key**
> 這樣切的 partition 時需要注意
> 不常用就會忘記

今天，發現用 MySQL 子查詢 partition table
explain 發現會吃免一個 partition 

```sql
SELECT 
xxx,
(SELECT count(1) FROM xxxx WHERE id = a.id) as count
FROM a
where id = 'xxx'
```

目前不確定是不是因為 id 為字串關系
但目前系統查詢還不會很慢


[MySQL 之 KEY 分區引發的血案 - 每日頭條](https://kknews.cc/other/mjzoq52.html)

## SQL 查詢 Partition 語法

```sql
SELECT * FROM information_schema.partitions WHERE TABLE_SCHEMA='your_database' AND TABLE_NAME = 'your_table' AND PARTITION_NAME IS NOT NULL
```

[Are the MySQL Partitions visible in the EXPLAIN plan of a SELECT....? - Stack Overflow](https://stackoverflow.com/questions/8700613/are-the-mysql-partitions-visible-in-the-explain-plan-of-a-select)

## EXPLAIN 沒有出現 Partition

用`EXPLAIN PARTITIONS`

[Are the MySQL Partitions visible in the EXPLAIN plan of a SELECT....? - Stack Overflow](https://stackoverflow.com/questions/8700613/are-the-mysql-partitions-visible-in-the-explain-plan-of-a-select)

[mysql - How to view information of partitions in a table? - Stack Overflow](https://stackoverflow.com/questions/25508419/how-to-view-information-of-partitions-in-a-table)

## Partition 分配不均的問題

用質數，我也沒有實驗過...
[MySQL 之 KEY 分區引發的血案 - 每日頭條](https://kknews.cc/other/mjzoq52.html) {% asset_link 2.png 備份圖 %}
[mysql 中的 key 分区为什么总是一半有数据，一半没有数据？-CSDN 论坛](https://bbs.csdn.net/topics/390857704)

```
如果設置40，64，128等偶數個分區數（PARTITIONS 64），會導致編號為奇數的分區（p1, p3, p5, p7, ... p2n-1）完全插不進數據；
如果設置63，121（PARTITIONS 63）這種奇數但非質數個分區數，所有分區都會有數據，但是不均勻；
如果設置137，31這種質數個分區數（PARTITIONS 137），所有分區都會有數據，並且非常均勻；


原文網址：https://kknews.cc/other/mjzoq52.html
```

## KEY & Hash 差異

[MySQL 分区总结 - iVictor - 博客园](https://www.cnblogs.com/ivictor/p/5033708.html)

看不是很懂，Hash 不能重覆,Key 可以??? 等研究出來再補

## 手建和內建 Partition 差別

> 手工分表 VS 分區表
> 手工分表的邏輯，找到所有需要更新的分表，然後依次更新，在性能上，與分區表並沒有實質的差別
> 分區表由 Server 層決定使用哪個分區，手工分表由應用代碼決定使用哪一個分表

[MySQL -- 分区表 | 点滴积累](http://zhongmingmao.me/2019/03/17/mysql-partition-table/) {% asset_link 1.png 備份圖 %}

## UUID 主鍵 vs 流水號 主鍵

[MySQL 使用自增 ID 主键和 UUID 作为主键的优劣比较详细过程（从百万到千万表记录测试） - chuixue24 的博客 - CSDN 博客](https://blog.csdn.net/chuixue24/article/details/90449075)

## 不是每張表適合做 Partition

也看 SQL 語句，通常要看所有 WHERE 條件資料依照做切割

## 刪除 Partition 會有資料消失??!

phpmyadmin 可以做刪除動作!!但發現會有少資料

解決方法

```SQL
ALTER TABLE xxxxxx rename TO old_20170101_xxxxxx;

CREATE TABLE `xxxxxx` (
ooooo
) ENGINE=InnoDB DEFAULT CHARSET=utf8
;

INSERT INTO xxxxxx SELECT * FROM old_20170101_xxxxxx;
```

不過為什麼會少資料還沒有找到原因


## 自動建立 Partition 

- [通过shell脚本自动增加mysql分区表的分区 - MySQL及其它开源数据库 - ITPUB论坛－中国专业的IT技术社区](http://www.itpub.net/thread-1417474-1-1.html)

<details>
  <summary>本文備份</summary>
```
#!/bin/sh
#
#
:<<BLOCK
######################################################################
SHELL_NAME：Logdb_Add_Partition.sh
  Functional Description：At the last month auto add the logdb table partition
    Argument：

            $1 USER Mysql Account
            $2 PASS Mysql Account Pass
            $3 DB        Mysql Logdb
    Version：V1.0
    Creater : SongYunkui
              Colin_Song
    Crete_time：2010/12/9
    Modify：1. MODIFY BY
            2. ADD BY ____ ___/__/__ Add:_________
######################################################################            
BLOCK

#######################################################################################
if [ $# -lt 3 ]; then
  echo "Please Input The Correct Args"
  echo "Usage Logdb_Add_Partition.sh <USER> <PASS> <DB>"
  exit -1
fi

USER=$1
PASS=$2
DB=$3

##config section begin
CONN_MYSQL="-u$USER -p$PASS -s"
MYSQL_HOME=/opt/modules/mysql
MYSQL_DIR=${MYSQL_HOME}/bin/mysql
SHELL_BASE=/opt/sbin/Logdb
LOG_DIR=${SHELL_BASE}/log
OPT_NAME=add_partition

MKDIR=`whereis -b mkdir|awk '{print $2}'`
TOUCH=`whereis -b touch|awk '{print $2}'`
DATE=`whereis -b date|awk '{print $2}'`
if [ ! -d ${SHELL_BASE} ]
then
    ${MKDIR} -p ${SHELL_BASE}
fi

if [ ! -d ${LOG_DIR} ]
then
    ${MKDIR} -p ${LOG_DIR}
fi

if [ ! -d ${INI_DIR} ]
then
    ${MKDIR} -p ${INI_DIR}
fi

LOG_FILE=${LOG_DIR}/${OPT_NAME}.log

#config section end

#working start
CURRENT_DATE=`${DATE} +'%Y-%m-%d'`
echo "${CURRENT_DATE} everything is ok, runing start" >> ${LOG_FILE}

#loop read the partition table and column
while read TAB_NAME COL_NAME
do

COUNTER=1
CURRENT_YEAR=`date +%Y`
#check the next month
NEXT_MONTH=`date -d next-month +%m`

#check the next month has many days
case ${NEXT_MONTH} in
            1|01|3|03|5|05|7|07|8|08|10|12)
                                           CURRENT_DAY=31
            ;;
            4|04|6|06|9|09|11)
                              CURRENT_DAY=30
            ;;
            2|02)
                if [ `expr ${CURRENT_YEAR} % 4` -eq 0 ]; then
                        if [ `expr ${CURRENT_YEAR} % 400` -eq 0 ]; then
                                CURRENT_DAY=29
                        elif [ `expr ${CURRENT_YEAR} % 100` -eq 0 ]; then
                                CURRENT_DAY=28
                        else
                                CURRENT_DAY=29
                        fi
                else
                        CURRENT_DAY=28
                fi
             ;;
esac

#work start add the every day partition
while [ ${COUNTER} -le ${CURRENT_DAY} ]

do

#calculate the current day's next {counter} day
PATNAME_DATE=`date -d "${COUNTER} days" +%Y%m%d`
COUNTER=`expr ${COUNTER} + 1`
PAT_DATE=`date -d "${COUNTER} days" +%Y%m%d`

#change the unix_timestamp
PAT_UNIX_TIMESTAMP=`${MYSQL_DIR} ${CONN_MYSQL} <<EOF
use ${DB};
select UNIX_TIMESTAMP('${PAT_DATE}');
EOF`

##add partition sql
V_SQL="ALTER TABLE ${DB}."${TAB_NAME}" ADD PARTITION (PARTITION P"${PATNAME_DATE}" VALUES LESS THAN ("${PAT_UNIX_TIMESTAMP}"));"
echo $V_SQL

#exec the sql
${MYSQL_DIR} ${CONN_MYSQL} <<EOF
use ${DB};
$V_SQL;
EOF

done
done<./Logdb_Partition.ini

#working end
END_DATE=`${DATE} +'%Y-%m-%d %H:%M:%S'`
echo "${END_DATE} runing finished" >> ${LOG_FILE}
echo -e "\n--------------------------------------------------------------------" >> ${LOG_FILE}

```
</details>

- [Partition maintainance script for Mysql – Database Administration Blog](https://www.dbavalley.com/partition-maintainance-script-for-mysql/)
- [mysql 能不能自动按日期分区（3个月如果手写要写90个partition） - OSCHINA](https://www.oschina.net/question/146658_55659)


<details>
  <summary>本文備份</summary>
```
 MYSQL-- 每半月一个分区，自动维护

分类： Mysql/postgreSQL

2010-11-09 15:52:12

建表语句
 

drop table if exists terminal_parameter;
CREATE TABLE `terminal_parameter` (
  `terminal_parameter_id` int(11) NOT NULL AUTO_INCREMENT,
  `serial` int(11) DEFAULT NULL,
  `network_type` char(1) DEFAULT NULL,
  `mcc` int(8) DEFAULT NULL,
  `mnc` int(8) DEFAULT NULL,
  `lac` int(8) DEFAULT NULL,
  `cellid` int(8) DEFAULT NULL,
  `bsic_psc` int(8) DEFAULT NULL,
  `ta_ec_io` int(8) DEFAULT NULL,
  `bcch_rxlev_rscp` int(8) DEFAULT NULL,
  `arfcn_uarfcn` int(8) DEFAULT NULL,
  `rxq` int(8) DEFAULT NULL,
  `c1` int(8) DEFAULT NULL,
  `c2` int(8) DEFAULT NULL,
  `signal_intensity` int(8) DEFAULT NULL,
  `error_rate` int(8) DEFAULT NULL,
  `alarm_type` varchar(16) DEFAULT NULL,
  `txpower` int(8) DEFAULT NULL,
  `small_running_number` int(8) DEFAULT NULL,
  `createtime` datetime NOT NULL,
  `userid` int(8) NOT NULL,
  `terminal_id` int(8) DEFAULT NULL,
  `state` char(1) DEFAULT '0',
  `order_definition_id` int(8) DEFAULT NULL,
  `order_code` varchar(20) DEFAULT NULL,
  `charg_voltage` float(8,2) DEFAULT NULL,
  `battery_voltage` float(8,2) DEFAULT NULL,
  `temprad` float(8,2) DEFAULT NULL,
  `run_state` int(8) DEFAULT NULL,
  `switching_value1` int(8) DEFAULT NULL,
  `switching_value2` int(8) DEFAULT NULL,
  `bcch_freq` int(8) DEFAULT NULL,
  `rxlev` int(8) DEFAULT NULL,
  `rxlev_full` int(8) DEFAULT NULL,
  `rxlev_sub` int(8) DEFAULT NULL,
  `rxqual` int(8) DEFAULT NULL,
  `rxqual_full` int(8) DEFAULT NULL,
  `rxqual_sub` int(8) DEFAULT NULL,
  `idle_ts` int(8) DEFAULT NULL,
  `timing_advance` int(8) DEFAULT NULL,
  `tch_efr_out` int(8) DEFAULT NULL,
  `tch_efr_in` int(8) DEFAULT NULL,
  `dtx` int(8) DEFAULT NULL,
  `major_cycle_frequency` int(8) DEFAULT NULL,
  PRIMARY KEY (`terminal_parameter_id`,`createtime`),
  KEY `idx_createtime` (`createtime`),
  KEY `idx_terminal_id` (`terminal_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
PARTITION BY RANGE(TO_DAYS (createtime))
(
PARTITION p20101115 VALUES LESS THAN (TO_DAYS('2010-11-15')),
PARTITION p20101130 VALUES LESS THAN (TO_DAYS('2010-11-30')),
PARTITION p20101215 VALUES LESS THAN (TO_DAYS('2010-12-15')),
PARTITION p20101231 VALUES LESS THAN (TO_DAYS('2010-12-31')),
PARTITION p20110115 VALUES LESS THAN (TO_DAYS('2011-01-15')),
PARTITION p20110131 VALUES LESS THAN (TO_DAYS('2011-01-31')),
PARTITION p20110215 VALUES LESS THAN (TO_DAYS('2011-02-15')),
PARTITION p20110228 VALUES LESS THAN (TO_DAYS('2011-02-28')),
PARTITION p20110315 VALUES LESS THAN (TO_DAYS('2011-03-15')),
PARTITION p20110331 VALUES LESS THAN (TO_DAYS('2011-03-31')),
PARTITION p20110415 VALUES LESS THAN (TO_DAYS('2011-04-15')),
PARTITION p20110430 VALUES LESS THAN (TO_DAYS('2011-04-30'))
);


存储过程代码：

 

* 每隔15天执行一次
/* 程序功能：循环使用分区，每半个月一个分区，保留6个月的数据
 时间：2010-11-09 */
 drop procedure if exists Set_Partition;
 create procedure Set_Partition()
 begin
/* 事务回滚，其实放这里没什么作用，ALTER TABLE是隐式提交，回滚不了的。*/
    declare exit handler for sqlexception rollback;
    start TRANSACTION;
 
/* 到系统表查出这个表的最大分区，得到最大分区的日期。在创建分区的时候，名称就以日期格式存放，方便后面维护 */
    select REPLACE(partition_name,'p','') into @P12_Name from INFORMATION_SCHEMA.PARTITIONS where TABLE_SCHEMA='mydb_1' and table_name='terminal_parameter' order by partition_ordinal_position DESC limit 1;

/* 判断最大分区的时间段，如果是前半个月的，那么根据情况需要加13,14,15,16天
   如果是后半个月的，那么直接加15天。 +0 是为了把日期都格式化成YYYYMMDD这样的格式*/
    IF (DAY(@P12_Name)<=15) THEN
       CASE day(LAST_DAY(@P12_name))
          WHEN 31 THEN set @Max_date= date(DATE_ADD(@P12_Name+0,INTERVAL 16 DAY))+0 ;
          WHEN 30 THEN set @Max_date= date(DATE_ADD(@P12_Name+0,INTERVAL 15 DAY))+0 ;
          WHEN 29 THEN set @Max_date= date(DATE_ADD(@P12_Name+0,INTERVAL 14 DAY))+0 ;
          WHEN 28 THEN set @Max_date= date(DATE_ADD(@P12_Name+0,INTERVAL 13 DAY))+0 ;
       END CASE;
    ELSE
       set @Max_date= date(DATE_ADD(@P12_Name+0, INTERVAL 15 DAY))+0;
    END IF;

/* 修改表，在最大分区的后面增加一个分区，时间范围加半个月 */
    SET @s1=concat('ALTER TABLE terminal_parameter ADD PARTITION (PARTITION p',@Max_date,' VALUES LESS THAN (TO_DAYS (''',date(@Max_date),''')))');
    PREPARE stmt2 FROM @s1;
    EXECUTE stmt2;
    DEALLOCATE PREPARE stmt2;

/* 取出最小的分区的名称，并删除掉 。
    注意：删除分区会同时删除分区内的数据，慎重 */
    select partition_name into @P0_Name from INFORMATION_SCHEMA.PARTITIONS where TABLE_SCHEMA='mydb_1' and table_name='terminal_parameter' order by partition_ordinal_position limit 1;
    SET @s=concat('ALTER TABLE terminal_parameter DROP PARTITION ',@P0_Name);
    PREPARE stmt1 FROM @s;
    EXECUTE stmt1;
    DEALLOCATE PREPARE stmt1;

/* 提交 */
    COMMIT ;
 end;
 


计划任务代码：

 

CREATE EVENT e_Set_Partition
          ON SCHEDULE
          EVERY 15 day STARTS '2011-04-30 23:59:59'
          DO
          call Set_Partition();
```

</details>

- [MYSQL-- 每半月一个分区，自动维护-909413335-ChinaUnix博客](http://blog.chinaunix.net/uid-24086995-id-127389.html)
- [Partition maintainance script for Mysql – Database Administration Blog](https://www.dbavalley.com/partition-maintainance-script-for-mysql/)


其他連結:
* [Partition maintainance script for Mysql – Database Administration Blog](https://www.dbavalley.com/partition-maintainance-script-for-mysql/)
* [MySQL partition table 初體驗 @ 不大會寫程式 :: 隨意窩 Xuite日誌](https://blog.xuite.net/misgarlic/weblogic/314570030-MySQL+partition+table+%E5%88%9D%E9%AB%94%E9%A9%97)
* [XYZ的筆記本: MySQL 資料表分區(partition)](https://xyz.cinc.biz/2015/07/mysql-partition.html)
* [What's MySQL partition](http://blog.kenyang.net/2017/06/11/whats-mysql-partition)
* [mysql 能不能自动按日期分区（3个月如果手写要写90个partition） - OSCHINA](https://www.oschina.net/question/146658_55659)
* [mysql - ERROR 1526 (HY000): Table has no partition for value 1426566990 - Stack Overflow](https://stackoverflow.com/questions/29694539/error-1526-hy000-table-has-no-partition-for-value-1426566990/29695312)
* [Mysql - 如何修改 partition 並還原資料 - Luka’s notes](https://codingluka.com/modify-and-restore-mysql-partition/)
* [MySQL Partition and InnoDB - CornelTEK - Medium](https://medium.com/corneltek/mysql-partition-and-innodb-c2b5982e3c04)
* [[高并发问题] MySQL 分区的情况下不能使用自增 ID，若使用 UUID 与时间做联合主键，数据量特大时，查询库效率如何？ | Laravel China 社区](https://learnku.com/laravel/t/6566/high-concurrence-problem-cant-use-self-increasing-id-when-mysql-partition-is-used-if-uuid-and-time-are-combined-with-primary-keys-how-effective-is-the-query-pool-when-the-amount-of-data-is-large)
* [mysql 的 partition与key的关系、限制 - barfoo的专栏 - CSDN博客](https://blog.csdn.net/barfoo/article/details/4242863)
* [聊聊partition的方式 - code-craft - SegmentFault 思否](https://segmentfault.com/a/1190000011704687)
* [MySQL Partitioning a VARCHAR(60) - Stack Overflow](https://stackoverflow.com/questions/47423873/mysql-partitioning-a-varchar60)
* [［MySQL］MySQL分区与传统的分库分表 - 海天的笔记本 | haitian-coder](http://haitian299.github.io/2016/05/26/mysql-partitioning/)
* [MySQL partition 筆記 - Kakashi's Blog](https://kkc.github.io/2017/07/07/mysql-partitioning/)