---
title: Homestead 架設 Laravel 環境小記
date: 2019-06-16 19:35:59
tags: [larvel, vagrant]
categories: Laravel
---

小記 Homestead 使用方法

<!--more-->

## 安裝 vagrant 和 Virtualbox

參考:[第 15 天：以 Vagrant 建置 PHP/Laravel 開發環境 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10193858)

### VirtualBox

安裝可以直接`apt install virtualbox`
但這邊網路上是直接抓 deb

```bash
sudo apt install ./virtualbox-5.2_5.2.4-119785~Ubuntu~xenial_amd64.deb
```

### vagrant

去官網抓 deb

```bash
sudo apt install ./vagrant_2.0.1_x86_64.deb
```

我有用 rubygem 安裝
但就是不能用，用 deb 安裝會比較順利

## 安裝 Homestead

```bash
vagrant box add laravel/homestead
```

## Homestead 設定工具

```bash
git clone https://github.com/laravel/homestead.git Homestead
// 可以用 git tag 和有哪些版本
git checkout v8.5.1
```

首先說一下，沒設定 ssh 金鑰要先執行
`ssh-keygen -t rsa -b 4096 -C "{您的 Email}"` 指令產生金鑰。
是為了`vagrant ssh`用的

更詳細設定
[第 15 天：以 Vagrant 建置 PHP/Laravel 開發環境 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10193858)

## 修改 Homestead.yaml

首先，在 homestead 目錄執行`bash init.sh`
設定 Homestead.yaml

```yaml
---
ip: "192.168.10.10"
memory: 2048
cpus: 2
provider: virtualbox

authorize: ~/.ssh/id_rsa.pub

keys:
  - ~/.ssh/id_rsa

folders:
  - map: ~/quickstart2
    to: /home/vagrant/code

sites:
  - map: homestead.test
    to: /home/vagrant/code/public

databases:
  - homestead
# ports:
#     - send: 50000
#       to: 5000
#     - send: 7777
#       to: 777
#       protocol: udp
```

主要設定有 folders
要把 map 設定自己程式路徑
to 不需要調整

有更多安裝可以看官網文件
[Laravel Homestead - Laravel - The PHP Framework For Web Artisans](https://laravel.com/docs/5.8/homestead#installing-elasticsearch)

執行 Homestead
`vagrant ssh`

再做

```
composer install
php artisan migrate
```

這時候自己的`http://localhost:8000`
可以看到東西

## 一些 homestead.yml 設定

```yml
databases:
  - homestead
  - test1
```

vagrant 執行的時候會建立 databases
算是滿方便的功能

帳號/密碼
user: homestead
pass: secret

[Laravel5.1/Homestead (0.2.7) 开发环境的部署和设置 | Laravel China 社区](https://learnku.com/laravel/t/1245/laravel51homestead-027-development-environment-deployment-and-settings)
[Laravel Homestead：所有配置详解 | Laravel China 社区](https://learnku.com/laravel/wikis/25530)
[Laravel Homestead：设置 Cron 计划任务 | Laravel China 社区](https://learnku.com/laravel/wikis/25541)

簡單完成 Laravel Task 架設
[laravel/quickstart-basic at 0f8346968b2121edf4ac70bf0183821a87929b19](https://github.com/laravel/quickstart-basic/tree/0f8346968b2121edf4ac70bf0183821a87929b19)
[中级任务清单 |《Laravel 5.1 中文文档》| Laravel China 社区](https://learnku.com/docs/laravel/5.1/quickstart-intermediate/1049)

[Laravel Cron 定时任务 “跳坑” 点 | Laravel China 社区](https://learnku.com/articles/18697)
