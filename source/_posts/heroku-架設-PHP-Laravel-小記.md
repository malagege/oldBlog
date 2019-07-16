---
title: heroku 架設 PHP & Laravel  小記
date: 2019-06-30 19:55:47
tags: [heroku, deploy]
categories: Heroku
---

最近想寫一些程式放在網路上
但目前應該只有想測試程式用
網路有看到 Heroku
所以今天實作看看

<!--more-->

## 官網教學

[Getting Started on Heroku with PHP | Heroku Dev Center](https://devcenter.heroku.com/articles/getting-started-with-php)

1. 準備

- heroku cli 安裝
- php
- composer

2. cli 登入

`heroku login`

3. 安裝測試 project

```bash
git clone https://github.com/heroku/php-getting-started.git
cd php-getting-started
```

4. 建制部屬分支

```bash
heroku create
```

在下這個指令之前
請看現在`git remote`分支
只會有一個 master
但是打了`heroku create`
會多出一個分支，那個是部屬用的

5. 推送程式上去

```bash
git push heroku master
```

6. `heroku open`指令會跳出瀏覽器，可看到部屬成果

**其實這邊算部屬完了**

7. 看 log

```bash
heroku logs --tail
```

8. Procfile 設定
   其實看教學完後，我一直在想 Heroku 怎麼知道我不屬哪一個程式語言?
   後來我看到這個，才知道為什麼 Heroku 能跑出我要的東西
   為什麼自動部屬可以跑 composer?
   我覺得應該是這個關係 [heroku-buildpack-php/heroku-php-apache2 at master · heroku/heroku-buildpack-php](https://github.com/heroku/heroku-buildpack-php/blob/master/bin/heroku-php-apache2)

```bash
web: vendor/bin/heroku-php-apache2 web/
```

9. 更新現有程式內容

這邊我就不詳細記錄了
文件建議 vendor 要加在.gititnore 裡面

```bash
composer require ...
composer update
git add .
git commit -m 'demo'
git push heroku master
heroku open xxx
```

10 操作 heroku cli

heroku run "php -a"

```
heroku run bash
```

11. 設定環境變數

```bash
// 設定環境變數
heroku config:set TIMES=20
// 看 heroku env
heroku config
```

12. 建置 Postgresql

```bash
heroku addons:create heroku-postgresql:hobby-dev

```

環境變數會多一個 `DATABASE_URL`

```php
$dbopts = parse_url(getenv('DATABASE_URL'));
$app->register(new Csanquer\Silex\PdoServiceProvider\Provider\PDOServiceProvider('pdo'),
               array(
                'pdo.server' => array(
                   'driver'   => 'pgsql',
                   'user' => $dbopts["user"],
                   'password' => $dbopts["pass"],
                   'host' => $dbopts["host"],
                   'port' => $dbopts["port"],
                   'dbname' => ltrim($dbopts["path"],'/')
                   )
               )
);

```

網路也有茶道可以使用[Heroku 連線 Postgresql 教學 ~ 度估記事本](https://daimom3020.blogspot.com/2018/05/heroku-postgresql.html)
去做 DB 管理

---

小記我 Heroku 有 rails 專案

之前 2014 年有參加 rails 新手活動
結果我今天用都忘記我有用過這個...

要怎麼把上面舊專案下載下來呢

```bash
heroku git:clone -a heroku_project
```

## Laravel

之後 Laravel 架設可能需要調整到

1. [如何利用 Heroku 免費將本地 Laravel 專案部署到線上，已解決 - IT 閱讀](https://www.itread01.com/content/1546353248.html)
2. [laravel-heroku-example/readme.md at master · jessedc/laravel-heroku-example](https://github.com/jessedc/laravel-heroku-example/blob/master/readme.md)
3. [如何快速使用 Heroku 来部署你的 Laravel 应用 | Laravel China 社区](https://learnku.com/articles/2602/how-to-quickly-use-heroku-to-deploy-your-laravel-application)

先記錄連結，有空寫寫我的部屬過程

## 更多 heroku cli 指令

[Heroku 運行類別、 Procfile、常用指令筆記 | DEVLOG of andyyou](https://andyyou.github.io/2016/10/31/process-types-and-profile/)
