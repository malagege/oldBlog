---
title: vagrant 搭配使用 ansible 和 docker 架設 redis 小記
date: 2020-02-15 18:43:43
tags: [vagrant,docker,ansible]
categories: Vagrant
---

常常我們需要做布署安裝
多台可能需要
最近看到 vagrant 可以搭配 ansible 使用
網路上隨便找一個範例小記
意外發現 `/var/run` 一些程式設定方法

<!--more-->

## vagrant 使用 ansible 安裝 redis

[mikeblum/redis-playbook: Ansible playbook for deploying, securing, and optimizing a Redis instance](https://github.com/mikeblum/redis-playbook)

```bash
git clone https://github.com/mikeblum/redis-playbook.git
cd redis-playbook
# image 可以改成用過的
vagrant up
```

這時候 redis 都是可以用的
但我發現 vagrant reload 重開機後
redis 竟然開不起來

### /var/run 引發 redis 無法啟動

`/var/run` 裡面的 redis 重開機竟然就不見了
經過 Google 發現由於 ubuntu 預設 /var/run 是用 tmpfs
所以重開機資料就不會在

但我發現裡面有 docker ,mysqld...，權限也不是 root
這又是從哪裡來的呢?

後來追查發現 `/etc/init/` 裡面 docker.conf 和 mysql.conf 有做產生檔案改權限相關動作

> 題外話不過那 `/var/log` 是怎麼改的?
> 這也是不錯的問題

但是我嘗試使用 apt install redis 
發現 /etc/init 沒有我要的
後來發現 /etc/init.d/ 裡面有相關 redis 設定

```bash
###########################
#chkconfig: 2345 10 90
#description: Start and Stop redis
PATH=/usr/local/bin:/sbin:/usr/bin:/bin
 
REDISPORT=6379
EXEC=/usr/local/bin/redis-server
REDIS_CLI=/usr/local/bin/redis-cli
##判断redis是否启动了
PIDFILE=/var/run/redis_6379.pid
CONF="/etc/redis.conf"
PASSWORD=$(cat $CONF|grep '^\s*requirepass'|awk '{print $2}'|sed 's/"//g')
 
case "$1" in
    start)
        if [ -f $PIDFILE ]
        then
                echo "$PIDFILE exists, process is already running or crashed"
        else
                echo "Starting Redis server..."
                $EXEC $CONF
        fi
        if [ "$?"="0" ]
        then
              echo "Redis is running..."
        fi
        ;;
    stop)
        if [ ! -f $PIDFILE ]
        then
                echo "$PIDFILE does not exist, process is not running"
        else
                PID=$(cat $PIDFILE)
                echo "Stopping ..."
if [ -z $PASSWORD ]
then
    $REDIS_CLI -p $REDISPORT shutdown
else
    $REDIS_CLI -a $PASSWORD -p $REDISPORT shutdown
fi
                #$REDIS_CLI -p $REDISPORT SHUTDOWN
                while [ -x ${PIDFILE} ]
               do
                    echo "Waiting for Redis to shutdown ..."
                    sleep 1
                done
                echo "Redis stopped"
        fi
        ;;
   restart|force-reload)
        ${0} stop
        ${0} start
        ;;
  *)
    echo "Usage: /etc/init.d/redis {start|stop|restart|force-reload}" >&2
        exit 1
esac
##############################
```
我懶得從 VM 搬出來，參考[Redis在Linux上的安装及相关配置 - 知乎](https://zhuanlan.zhihu.com/p/73464692)

但就沒有專門給 systemctl 設定用的嗎?

還是有找到方法，但很多都沒有提到
是不是大部分 /var/run 沒有掛載到 tmpfs 上面??
```
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
PermissionsStartOnly=true
ExecStartPre=-/bin/mkdir -p /var/run/redis
ExecStartPre=-/bin/chown redis.redis /var/run/redis
#PIDFile=/var/run/redis_6379.pid  發現沒用也
User=redis
Group=redis
ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/local/bin/redis-cli shutdown
Restart=always


[Install]
WantedBy=multi-user.target
```

上面 PIDFile 好像沒加也能正常跑( 可以看 /etc/redis/redis.conf 的 pidfile )
主要上 `/var/run/redis` 資料夾需要建立，並且權限給 redis:redis

不過這個算是最佳解嗎?
我在這篇[/var/run/redis_2323 directory cannot be created in systemd service · Issue #132 · DavidWittman/ansible-redis](https://github.com/DavidWittman/ansible-redis/issues/132)看到
最後解決方案[Merge pull request #135 from DavidWittman/132-systemd-tmpfiles · DavidWittman/ansible-redis@9937b2e](https://github.com/DavidWittman/ansible-redis/commit/9937b2e5ec8c371f77f384b8252049e3344ffce7)使用 `/etc/tmpfiles.d`
不過我看我現在 linux 系統沒有這個目錄

### /etc/tempfiles.d 最佳解法

[systemd (正體中文) - ArchWiki](https://wiki.archlinux.org/index.php/Systemd_(%E6%AD%A3%E9%AB%94%E4%B8%AD%E6%96%87)#%E8%87%A8%E6%99%82%E6%AA%94%E6%A1%88)

> /usr/lib/tmpfiles.d/ 和 /etc/tmpfiles.d/ 中的檔案描述了 systemd-tmpfiles 如何創建、清理、删除臨時檔案和目錄，這些檔案和目錄通常存放在 /run 和 /tmp 中。設定檔名稱為 /etc/tmpfiles.d/<program>.conf。此處的配置能覆蓋 /usr/lib/tmpfiles.d/目錄中的同名配置。
> 臨時檔案通常和服務檔案同时提供，以生成守護程序需要的檔案和目錄。例如 Samba 服務需要目錄 /run/samba 存在並設置正確的權限位

[Ubuntu 16.04 在 reboot 後保留 tmp 內的檔案 | Mr. 沙先生](https://shazi.info/ubuntu-14-04-16-04-%E5%9C%A8-reboot-%E5%BE%8C%E4%BF%9D%E7%95%99-tmp-%E5%85%A7%E7%9A%84%E6%AA%94%E6%A1%88/)

我 Elementary OS 裡面 `/etc/tmpfiles.d/` 沒有任何東西，`/usr/lib/tmpfiles.d/` 底下有
不過正常應該是可以用的

只要在 playbook.yml 下面加

```yml
    - name: create systemd tmpfiles configuration
      template: src=templates/tmpfiles_redis.conf.j2
                dest=/etc/tmpfiles.d/redis.conf
                mode=0644 
```

templates/tmpfiles_redis.conf.j2
```
d {{ redis_socket|dirname }} 0755 {{ redis_user }} {{ redis_group }}
```

就可以正常了

### 小結

我使用這個 playbook 不是非常完整
但他是我第一個網路上找到可以跑的 playbook
而且配置上還滿清楚的

比較多完整配置可以用[DavidWittman/ansible-redis: Highly-configurable Ansible role to install Redis and Redis Sentinel from source](https://github.com/DavidWittman/ansible-redis)

## linux install 指令

[install command in Linux with examples - GeeksforGeeks](https://www.geeksforgeeks.org/install-command-in-linux-with-examples/)

簡單來說 可以複製檔案並且設定權限
這是我從 /etc/init/mysql.conf 看到
由於上面我花了一堆時間，這個就先跳過...

## vagrant 搭配 docker

參考[使用Vagrant跟Docker快速建構開發與測試環境 « Sam的程式筆記](http://samchu.logdown.com/posts/288889-docker-fast-with-vagrant-and-construct-development-and-test-environments)


執行結果
```
Bringing machine 'elasticsearch' up with 'docker' provider...
Bringing machine 'postgres' up with 'docker' provider...
==> elasticsearch: Pruning invalid NFS exports. Administrator privileges will be required...
[sudo] password for malagege:     
==> elasticsearch: Creating the container...
    elasticsearch:   Name: elasticsearch
    elasticsearch:  Image: elasticsearch
    elasticsearch:   Port: 9200:9200
    elasticsearch:   Port: 9300:9300
A Docker command executed by Vagrant didn't complete successfully!
The command run along with the output from the command is shown
below.

Command: ["docker", "run", "--name", "elasticsearch", "-d", "-p", "9200:9200", "-p", "9300:9300", "elasticsearch", {:notify=>[:stdout, :stderr]}]

Stderr: Unable to find image 'elasticsearch:latest' locally
/usr/bin/docker: Error response from daemon: manifest for elasticsearch:latest not found: manifest unknown: manifest unknown.
See '/usr/bin/docker run --help'.
```

感覺電腦外層要裝 docker
~~NFS 掛載你的 docker unix socket 到裡面開 docker container~~
他就只是在 docker 開啟 container...
不過這樣有什麼好處... 還在想

所以不能直接`vagrant ssh`
需要使用 `vagrant docker-*` 相關指令
[drupal - Vagrant ssh to Docker container - Stack Overflow](https://stackoverflow.com/questions/37191371/vagrant-ssh-to-docker-container)

當初以為這是在 VM 裝 docker，直接抓 image 開啟 container
但跟我想的不一樣

老實說這有什麼好處，我想可能用 docker-compose 會比較快吧...
但意外發現載入速度滿快的!!!(這不是廢話)

### vagrant ssh 登入 container

參考: [如何配置 Vagrant SSH 在 Docker Provider - iWonder Blog](http://blog.iwonder.tw/2017/08/20/vagrant-ssh-docker/)
有找到如何使用 vagrant ssh 進去

dockerfile
```dockerfile
# Pull base image
FROM ubuntu:16.04
# Installation
RUN apt-get update
RUN apt-get install -y openssh-server sudo
# Create vagrant user with sudo privilege
RUN useradd -m -d /home/vagrant -s /bin/bash vagrant
RUN echo 'vagrant:vagrant' | chpasswd
RUN echo 'vagrant ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers.d/vagrant
RUN chmod 440 /etc/sudoers.d/vagrant
# Run SSH service
RUN mkdir /var/run/sshd
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
```

Vagrantfile
```ruby
# Vagrantfile
# -*- mode: ruby -*-
Vagrant.configure("2") do |config|
  config.vm.provider "docker" do |d|
    d.build_dir = "."
    d.has_ssh = true
  end
  config.ssh.password = 'vagrant'
end
```

或

Vagrantfile
```ruby
# Vagrantfile
# -*- mode: ruby -*-
Vagrant.configure("2") do |config|
  config.vm.provider "docker" do |d|
    d.image = "wonderchang/baseimage-ssh"
    d.has_ssh = true
  end
  config.ssh.password = 'vagrant'
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y nginx
    service nginx start
  SHELL
end
```

vagrant up 就可以使用，vagrant ssh 就可以使用了


### 相關 vagrant 連結

[怎麼找到 Vagrant box 的 ssh 使用者與密碼 | Mr. 沙先生](https://shazi.info/%E6%80%8E%E9%BA%BC%E6%89%BE%E5%88%B0-vagrant-box-%E7%9A%84-ssh-%E4%BD%BF%E7%94%A8%E8%80%85%E8%88%87%E5%AF%86%E7%A2%BC/)
[Vagrant 學習筆記 - VagrantPi Blog](https://vagrantpi.github.io/2018/02/11/vagrant/)


Using default tag: latest
Error response from daemon: manifest for elasticsearch:latest not found: manifest unknown: manifest unknown

docker pull elasticsearch:6.5.0
[Elasticsearch latest not available · Issue #215 · elastic/elasticsearch-docker](https://github.com/elastic/elasticsearch-docker/issues/215)

[如何配置 Vagrant SSH 在 Docker Provider - iWonder Blog](http://blog.iwonder.tw/2017/08/20/vagrant-ssh-docker/)



[Ansible install and introduction | SAM的程式筆記](https://blog.samchu.dev/2019/07/05/Ansible-install-and-introduction/)