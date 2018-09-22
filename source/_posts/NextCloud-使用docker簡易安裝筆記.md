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