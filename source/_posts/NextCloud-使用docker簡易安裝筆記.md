---
title: NextCloud 使用docker簡易安裝筆記
date: 2018-09-16 00:59:32
tags: [docker,nas,nextcloud]
categories: Docker
---
NextCloud 使用docker簡易安裝筆記

<!--more-->
```
docker  run  -d  -p  8000:80   nextcloud
```

就可以簡單使用NextCloud

```
docker  run -d  -v  /mnt/HD1/nextcloud_bk:/var/www/html -p  8000:80 nextcloud
```
當然要備份資料可以掛載，我有試著掛載`~/data/owncolud.db`好像沒有作用
在想應該還有其他設定檔

它還有很特別功能，竟然有檔案版本控制
我沒玩過NAS，不知道是不是這是NAS基本功能
{% asset_img 01.png %}


家裡有一台Raspberry PI，它被用來當動物機，常常追番用的
平常傳資料我都是在window開hfs去傳的，有時候覺得跑來跑去很麻煩
還在想它可以怎麼跟Raspbery PI用

## elementary OS client設定

{% asset_img 1.png  %}

```
sudo add-apt-repository ppa:nextcloud-devs/client
sudo apt-get update
```
這樣就能安裝 nextcloud-client 
```
sudo apt-get install nextcloud-client
```

{% asset_img 2.png  %}

{% asset_img 3.png  %}

{% asset_img 4.png "沒有申請憑證，當然要點第2個選項" %}

可惜...，當天講者電腦icon有同步圖示，elementary OS好像看不到QQ

後來安裝nautilus跟nautilus-nextcloud，然後登入再登入之後就能看到nautilus有圖示功能了
```
sudo apt-get install nautilus nautilus-nextcloud
```
原生elementary os 檔案管理工具沒有套件....
{% asset_img files_icons.png  %}

找個播放器來試試

##  安裝audio player

可用上面搜尋快速找到你要的套件
{% asset_img 001.png  %}

{% asset_img 002.png "這邊不能用localhost連線，要用127.0.0.1(本機的話)" %}
{% asset_img 003.png  %}
{% asset_img 004.png  %}
{% asset_img 005.png  %}
{% asset_img 006.png  %}
{% asset_img 007.png  %}

別對搜尋中按鈕騙了...，害我等了快30分鐘...(我還以為壞掉了)
*請記得要點「搜尋中」他才會真的搜尋!!!*

後來還發現還有markdown套件!!!

# 假如區網可能會遇到非安全網域的問題

假如你有root權限，直接做volumes資料夾那邊設定
沒的話要`docker exec -ti xxxx bash`進去改

我沒看文件，加入 `1 => '*',`
竟然能跑XD
有對外開放IP，不建議這樣設定
指定比較安全

{% asset_img config.png  %}


參考來源:
[講者簡報](http://bit.ly/sakana20180915)
[講者blog](http://sakananote2.blogspot.com/)
[Promethues](https://docs.google.com/presentation/d/1aZzPbmhYfa51MsoK4FskQPeoIL6AqMXVzW5eQRiTfhw/edit#slide=id.g426e3e8d8f_1_0)
[Ubuntu 安裝教學](https://www.techrepublic.com/article/how-to-install-the-nextcloud-client-on-ubuntu/)


## 意外升級搞壞(2021-01-10)

今天用[Nextcloud Security Scan](https://scan.nextcloud.com/)掃我 nextcloud 弱點
發現版本稍微舊
[ownyourbits/nextcloudpi - Docker Hub](https://hub.docker.com/r/ownyourbits/nextcloudpi)
想說 `docker pull ownyourbits/nextcloudpi` 這樣更新
重啟新的 container 就可以了
誰知道 MariaDB 不能用了

不知道是不是之前版本是 MySQL 關係
於是我只能重啟重建之路

先假設我有使用 traefik 
所以我的 80 443 port 都被占用

這次我採取使用 docker-compose 來做

** 以下我用1.34.1 操作還是有問題**
有看到有人降到 1.31 就能正常使用
好像後面有大改版，畫面首頁有調整

1.34.1
[Docker volume does not contain user data - ℹ️ Support / 📦 Appliances (Docker, Snappy, VM, NCP) - Nextcloud community](https://help.nextcloud.com/t/docker-volume-does-not-contain-user-data/101808/25)
重點好像有bug，docker hub 好像沒這個版本
所以去 Git 上面抓下來編譯

```sh
sudo apt update
sudo apt install jq

git clone https://github.com/nextcloud/nextcloudpi.git  # 預設就是 1.34.1
git checkout v1.31
cd nextcloudpi
./build-docker.sh armhf
```


[nextcloudpi/docker-compose-armhf.yml at master · nextcloud/nextcloudpi · GitHub](https://github.com/nextcloud/nextcloudpi/blob/master/docker-compose-armhf.yml)
```yml
version: "3"

services:
  nextcloudpi:
#    image:  ownyourbits/nextcloudpi
#    後來發現 nextcloudpi 有 nextcloudpi-armhf image
    image: ownyourbits/nextcloudpi-armhf
    command: "192.168.1.x"
    networks:
        - traefik_network
    labels:
      - "traefik.backend=ncp"
      - "traefik.http.routers.ncp.rule=Host(`xxxx.duckdns.org`)"
      - "traefik.http.routers.ncp.tls=true"
      - "traefik.http.services.ncp.loadbalancer.server.port=80"
    restart: always
    ports:
      - "1180:80" # 暫時使用，traefik 可以註解調
      - "11443:443" # 暫時使用，traefik 可以註解調
      - "4443:4443"
    volumes:
      - /media/USBdrive/ncp2:/data
      - /etc/localtime:/etc/localtime:ro

networks:
  traefik_network:
    external: true

```


因為我用 Traefik 轉 https 關係
所以我要把 Force HTTPS 給關掉
nc-trust-domain 把 192.168.1.202 加進去

https://192.168.1.202:11443 就能用 ncp 進去
我通常會建立新的 admin 帳號給自己

要把舊的檔案一進去

```sh
docker-compose exec nextcloudpi bash

cd /data/nextcloud/

sudo -u www-data php occ files:scan --all

```

~~新版檔案路徑在 `/data-ro/nextcloud/data/xxuser/files`~~ 1.43.1 bug??

上傳大量檔案推薦使用 WinSCP 上傳
可以參考:[Harry 程式筆記: [WinSCP] sftp下使用sudo上傳檔案](https://harry-lin.blogspot.com/2019/05/winscp-sftpsudo.html)
怎麼使用 winscp? 可以參考我這篇 [WinSCP 用 sudo 上傳檔案的方法(含切換一般使用者) | 程式狂想筆記](https://malagege.github.io/blog/2020/01/10/WinSCP%20%E7%94%A8%20sudo%20%E4%B8%8A%E5%82%B3%E6%AA%94%E6%A1%88%E7%9A%84%E6%96%B9%E6%B3%95/)

https://192.168.1.x:4443/?app=nc-scan-auto

可以馬上執行

通常60分會讀一次

暫時收工


