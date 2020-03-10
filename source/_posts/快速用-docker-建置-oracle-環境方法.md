---
title: 快速用 docker 建置 oracle 環境方法
date: 2020-03-07 01:14:59
tags: [docker,oracle]
categories: Docker
---

最近要看 DB 工具是否能正常連 Oracle
簡單使用 Docker 來架設環境

<!--more-->

[componhead/docker-oracle-xe-11g: My oracle xe 11g](https://github.com/componhead/docker-oracle-xe-11g)
build 上面這版`docker build . -t oracle-xe-11g`

帳號密碼 system,oracle
這樣就能進去了

```yml
version: '2'
services:
  oracle:
    image: oracle-xe-11g
    restart: always   #如果docker容器由于一些问题挂掉的化，docker-composer会自动把容器给启动起来
    ports:
      - 1521:1521
      - 8080:8080
```

docker-compose up -d 就可以執行環境，但要等一下時間

docker-compose logs -f 看到最後一段就可以使用了

```
oracle_1  | ls: cannot access /u01/app/oracle/oradata: No such file or directory
oracle_1  | Database not initialized. Initializing database.
oracle_1  | Setting up:
oracle_1  | processes=500
oracle_1  | sessions=555
oracle_1  | transactions=610
oracle_1  | If you want to use different parameters set processes, sessions, transactions env variables and consider this formula:
oracle_1  | processes=x
oracle_1  | sessions=x*1.1+5
oracle_1  | transactions=sessions*1.1
oracle_1  | 
oracle_1  | Oracle Database 11g Express Edition Configuration
oracle_1  | -------------------------------------------------
oracle_1  | This will configure on-boot properties of Oracle Database 11g Express 
oracle_1  | Edition.  The following questions will determine whether the database should 
oracle_1  | be starting upon system boot, the ports it will use, and the passwords that 
oracle_1  | will be used for database accounts.  Press <Enter> to accept the defaults. 
oracle_1  | Ctrl-C will abort.
oracle_1  | 
oracle_1  | Specify the HTTP port that will be used for Oracle Application Express [8080]:
oracle_1  | Specify a port that will be used for the database listener [1521]:
oracle_1  | Specify a password to be used for database accounts.  Note that the same
oracle_1  | password will be used for SYS and SYSTEM.  Oracle recommends the use of 
oracle_1  | different passwords for each database account.  This can be done after 
oracle_1  | initial configuration:
oracle_1  | Confirm the password:
oracle_1  | 
oracle_1  | Do you want Oracle Database 11g Express Edition to be started on boot (y/n) [y]:
oracle_1  | Starting Oracle Net Listener...Done
oracle_1  | Configuring database...Done
oracle_1  | Starting Oracle Database 11g Express Edition instance...Done
oracle_1  | Installation completed successfully.
oracle_1  | Database initialized. Please visit http://#containeer:8080/apex to proceed with configuration
oracle_1  | Oracle Database 11g Express Edition instance is already started
oracle_1  | 
oracle_1  | Starting import from '/docker-entrypoint-initdb.d':
oracle_1  | found file /docker-entrypoint-initdb.d//docker-entrypoint-initdb.d/*
oracle_1  | [IMPORT] /entrypoint.sh: ignoring /docker-entrypoint-initdb.d/*
oracle_1  | 
oracle_1  | Import finished
oracle_1  | 
oracle_1  | Database ready to use. Enjoy! ;)
```


## 其他可參考設定

[docker安装wnameless/oracle-xe-11g并运行（手写超详细） - 哈喽，小枫 - 博客园](https://www.cnblogs.com/Jin520304/p/7884441.html)
[使用 Docker Oracle | Zoctan's Blog](https://zoctan.github.io/2018/05/21/%E4%BD%BF%E7%94%A8%E8%BF%87%E7%A8%8B/%E4%BD%BF%E7%94%A8%20Docker%20Oracle/)

[acktsw/oracle-xe-11g Dockerfile - Docker Hub](https://hub.docker.com/r/acktsw/oracle-xe-11g/dockerfile)
這個不知道為什麼帳號密碼無法登入，不過 ssh server 設定可以當參考

[docker compose安装oracle_数据库_落叶飘零z的博客-CSDN博客](https://blog.csdn.net/qq_38270106/article/details/83271284)