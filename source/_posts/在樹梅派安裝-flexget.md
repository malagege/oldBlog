---
title: 在樹梅派安裝 flexget
date: 2020-08-11 21:33:52
tags: [flexget, linux]
categories: Linux
---

在樹梅派安裝 flexget
通常要裝 python 3.5以上
[更新系統flexget壞掉啦!! | 程式狂想筆記](https://malagege.github.io/blog/2018/01/07/logdown/2018-01-07-4726929/)
雖然這篇有小記
但最近我看官方安裝方法有點不太一樣
這邊小記安裝紀錄

<!--more-->

## 安裝 flexget 

### 安裝 Pytnon 3.8

apt 無法取得
所以需要手動編譯 Python
[How to install Python 3.8 on Raspberry Pi (Raspbian) - installvirtual](https://installvirtual.com/how-to-install-python-3-8-on-raspberry-pi-raspbian/)

```bash
sudo apt-get update

sudo apt-get install -y build-essential tk-dev libncurses5-dev libncursesw5-dev libreadline6-dev libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev libffi-dev tar wget vim

# 下載 Python
wget https://www.python.org/ftp/python/3.8.0/Python-3.8.0.tgz

# 安裝 Python
# 這過程會很久，我使用 PI 1 ，安裝大概快一整天才好，建議使用 tmux 安裝，以免關掉需要重來
sudo tar zxf Python-3.8.0.tgz
cd Python-3.8.0
sudo ./configure --enable-optimizations
sudo make -j 4
sudo make altinstall

# 檢查
python3.8 -V

# 修改 default 3.8 ，也可以不用做，直接輸入 python3.8
echo "alias python=/usr/local/bin/python3.8" >> ~/.bashrc
source ~/.bashrc

# clean
sudo rm -rf Python-3.8.0.tgz
sudo rm -rf Python-3.8.0
```

### 另外一種安裝 Python 

發現有另外一種方式安裝 Python 3.8
[Install Python 3.8.1 on Raspberry Pi (Raspbian) - installvirtual](https://installvirtual.com/install-python-3-8-1-on-raspberry-pi-raspbian/)
已經安裝過了
我就不測試這個方法
但下次可能會試試看

```bash
# apt 
sudo apt-get update
sudo apt-get install -y build-essential tk-dev libncurses5-dev libncursesw5-dev libreadline6-dev libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev libffi-dev tar wget vim

# pyenv
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc && echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
pyenv install 3.8.1
pyenv versions
```
方法感覺更簡單

### 安裝 flexget

參考[FlexGet](https://flexget.com/InstallWizard/Linux)這篇
使用 virtualenv 還滿有趣的
記錄一下

> Supported versions of Python:
>    3.6, 3.7
>    3.8 (untested, but should work)

官方建議安裝版本
不過我沒注意安裝 3.8
但是測試可以執行

```bash
# virtualenv
python3 -m venv ~/.flexget/   # 推薦大家改.flexget 因為這樣設定 service 比較不會麻煩
# Install FlexGet in the virtualenv
cd ~/.flexget/
bin/pip install flexget
# Running FlexGet from the virtualenv
source ~/.flexget/bin/activate
# flexget
~/.flexget/bin/flexget [options]
# 全局安裝
# sudo pip install flexget
```
config.yml 要先設定在 flexget 資料夾才能這常執行

**安裝 transmissionrpc**

``` bash
~/flexget/bin/pip3 install  transmissionrpc
# 假如跑出錯誤Transmissionrpc module version 0.11 or higher required
# 就是少了安裝這個
```

~~最下面有比較潮的設定~~
docker 後來有對 config 做調整
```yml config.yml
web_server: yes

templates:
  default:
    transmission:
      host: 192.168.1.203
      port: 9091
      username: admin
      password: userpwd
      add_paused: no
      path: /mnt/extHDD/Download/Mikata/201901/{{task}}
    free_space:
      path: /mnt/extHDD/
      space: 1000
    clean_transmission:
      host: localhost
      port: 9091
      username: admin
      password: userpwd
      finished_for: 4 days
      min_ratio: 1
    if:
      - "'季度全集' in category": reject
  olddownload:
    transmission:
      host: 192.168.1.203
      port: 9091
      username: malagege
      password: malagegeinnfu
      add_paused: no
      path: /mnt/extHDD/Download/Mikata/201810/{{task}}
    free_space:
      path: /mnt/extHDD/
      space: 1000
    clean_transmission:
      host: localhost
      port: 9091
      username: admin
      password: admin
      finished_for: 4 days
      min_ratio: 1
    if:
      - "'季度全集' in category": reject
  autoPass:
    transmission:
      host: 192.168.1.203
      port: 9091
      username: admin
      password: admin
      add_paused: yes
      path: /mnt/extHDD/Download/Mikata/201810/{{task}}
    if:
      - "'季度全集' in category": reject

tasks:
  刀劍神域 Alicization:
    rss:
      url: https://share.dmhy.org/topics/rss/sort_id/2/rss.xml?keyword=刀劍神域 Alicization+悠哈璃羽字幕社＆拉斯观测组+CHT
      other_fields: [category]
    accept_all: yes
    template: autoPass

schedules:
  # Run every 30 minutes on monday
  - tasks: "*"
    schedule:
      hour: "*/3"
```


### 執行 WebUI 介面
[FlexGet](https://flexget.com/Web-UI)
```bash
# 設定 web 密碼，切記要很嚴謹密碼
flexget web passwd <some_password>
# 背景執行程式
flexget daemon start --daemonize
```

WebUI config 可線上修改config.yml 而且還有除錯功能!!非常簡單!!

http://ip:5050
user:flexget
pwd: (自己設定)

這邊 task 需要執行成功一次後
才會出現!!!

注意這邊控制 flexget
需要再 flexget 資料夾路徑
```bash
cd ~/flexget
~/flexget/bin/flexget daemon stop
~/flexget/bin/flexget daemon start --daemon
# 2020-08-11 22:10:37 CRITICAL manager                       Failed to find configuration file config.yml
# 2020-08-11 22:10:37 INFO     manager                       Tried to read from: /home/pi, /home/pi/.flexget, /home/pi/.config/flexget

```

## 其他

### flexget service

[/lib/systemd/system/flexget.service](https://gist.github.com/billsuxx/e2cd75d4b254e1999309)
下面路徑需要調整
我是放在/etc/systemd/system/flexget.service
```ini
[Unit]
Description=Flexget Daemon
After=network.target

[Service]
Type=simple
User=pi
ExecStart= /home/pi/.flexget/bin/flexget daemon start
ExecStop= /home/pi/.flexget/bin/flexget daemon stop
ExecReload= /home/pi/.flexget/bin/flexget daemon reload

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
```

systemctl 直接 enable/disable 
[rclone 掛載硬碟小記 | 程式狂想筆記](https://malagege.github.io/blog/2019/10/14/rclone-%E6%8E%9B%E8%BC%89%E7%A1%AC%E7%A2%9F%E5%B0%8F%E8%A8%98/)
這裡面之前有紀錄 systemctl



## docker 執行 flexget

### 安裝 docker

```bash
curl -sSL https://get.docker.com/ | sh
sudo usermod -aG docker pi
# 重新登入
sudo systemctl restart docker
sudo systemctl enable docker
```
### docker 執行 flexget

之前這邊有寫過[Raspberry Pi 使用 Docker 架設 flexget | 程式狂想筆記](https://malagege.github.io/blog/2020/01/08/Raspberry-Pi-%E4%BD%BF%E7%94%A8-Docker-%E6%9E%B6%E8%A8%AD-flexget/)
後來就沒繼續使用(因為我不太會用)
PI 要裝 Python 編譯比較花時間
在想有沒有比較快的方法

pi 1 請使用 arm32v6 ，其他代請使用 armv7

```dockerfile

FROM arm32v7/python:3.8.5-alpine
MAINTAINER bren

RUN apk add --update \
  tzdata \
  ca-certificates \
  && pip install --no-cache-dir --disable-pip-version-check flexget transmissionrpc \
  && rm -rf /var/cache/apk/*

# Copy local files
COPY files/ /

# add default volumes
VOLUME /config /data
WORKDIR /config

# expose port for flexget webui
EXPOSE 3539 3539/tcp

# run init.sh to set uid, gid, permissions and to launch flexget
RUN chmod +x /scripts/init.sh
CMD ["/scripts/init.sh"]

```

docker build . -t malagege/flexget

`id pi`可以看當下 user id

```bash
docker run -d \
    --name=flexget \
    -restart=always \
    -p 5050:5050 \ # 注意 port
    -v "$PWD/data":/data \
    -v "$PWD/config":/config \
    -e FG_WEBUI_PASSWD=__複雜密碼__ \
    # -e /mnt/xxx:/mnt/xxx  有使用 free_space 要開這個
    # -e PUID=1000 \
    # -e PGID=1000 \
    -e TZ="Asia/Taipei" \
    malagege/flexget

```

[docker參數--restart=always的作用 - 開始認識 - 博客園](https://www.cnblogs.com/kaishirenshi/p/10396446.html)
>  docker參數--restart=always的作用
> 創建容器時沒有添加參數  --restart=always ，導致的後果是：當 Docker 重啟時，容器未能自動啟動
> 1、Docker 命令修改
> docker container update --restart=always 容器名字



[FlexGet](https://flexget.com/Cookbook/TorrentCleanup)




## bitbucket CI/CD 檢查設定

因為這個我有跟我朋友一起改設定檔
所以我有加了 CI/CD 防止修改錯誤




```yml
image: nikolaik/python-nodejs:python3.8-nodejs12

pipelines:
  default:
    - step:
        script: # Modify the commands below to build your repository.
          - npm install -g yaml-lint
          - yamllint config.yml
#          - npm install
#          - npm test
          - pip install --no-cache-dir --disable-pip-version-check flexget
          - flexget -V
          - flexget check
```

2018-02-14 10:28 WARNING  manager    Config line XX is likely missing ':' at the end
是 bug 所以先不用理會
參考:[FLexget使用记录 - linkthis blog](https://linkthis.me/2018/02/15/the-note-of-using-flexget/)

### Git 同步方法

最後更改需要執行`docker exec flexget flexget daemon reload-config`
載入最新設定檔案

```bash
#!/bin/bash

cd /home/pi/flexget-arm/config && git fetch origin master

LOCAL_COMMITID=$(git rev-parse HEAD)
echo "LCOAL_COMMITID=${LOCAL_COMMITID}"
REMOTE_COMMITID=$(git rev-parse origin/master)
echo "REMOTE_COMMITID=${REMOTE_COMMITID}"

if [ "$LOCAL_COMMITID" != "$REMOTE_COMMITID" ]
then
    echo "COMMITID不一致，需做同步"
    git reset origin/master --hard && docker exec flexget flexget daemon reload-config
fi
```


### 手動執行排程

docker exec flexget flexget execute  --tasks "TV-*"

config.yml
```yml
web_server: yes

templates:
  default:
    transmission:
      host: 192.168.1.203
      port: 9091
      username: admin
      password: admin
      add_paused: no
      path: /mnt/extHDD/Download/Mikata/TV/{{task}}
    free_space:
      path: /mnt/extHDD/
      space: 1000
    clean_transmission:
      host: 192.168.1.203
      port: 9091
      username: admin
      password: admin
      finished_for: 60 days
      min_ratio: 1
    if:
      - "'季度全集' in category": reject
  autoPass:
    transmission:
      host: 192.168.1.203
      port: 9091
      username: admin
      password: admin
      add_paused: yes
      path: /mnt/extHDD/Download/Mikata/autoPass/{{task}}
    if:
      - "'季度全集' in category": reject


tasks:
  "TV-刀劍神域 Alicization":
    rss:
      url: https://share.dmhy.org/topics/rss/sort_id/2/rss.xml?keyword=刀劍神域+悠哈璃羽字幕社＆拉斯观测组+CHT
      other_fields: [category]
    accept_all: yes
    template: default
    transmission:
      path: /mnt/extHDD/Download/Mikata/TV/202007/{{task[3:]}}

schedules:
  - tasks: "TV-*"
    schedule:
      day_of_week: mon,tue,wed,thu,fri,sat,sun
      hour: "3,5,8,11,13,15,17,20"
      jitter: 60
```