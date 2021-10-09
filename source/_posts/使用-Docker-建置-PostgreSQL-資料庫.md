---
title: 使用 Docker 建置 PostgreSQL 資料庫
date: 2021-10-09 23:15:42
tags: [docker,postgressql,deploy]
categories: Docker
---

docker hub 有範例，但我還是紀錄一下。

<!--more-->

```yaml=
# Use postgres/example user/password credentials
version: '3.1'

services:

  db:
    image: postgres
    restart: always
    environment:
      POSTGRES_PASSWORD: example

  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080

```



## add host


```
  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
    extra_hosts:
      - "oracle:162.242.195.82"
      - "finvc:172.31.70.174"


```


https://github.com/compose-spec/compose-spec/blob/master/spec.md#extra_hosts