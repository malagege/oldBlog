---
title: '安裝 ELK(ElasticSearch,Logstash,Kibana),filebeat 初體驗'
date: 2021-05-02 20:32:04
tags: [elasticsearch, logstash, kibana, filebeat]
categories: ELK
---


# 安裝 ELK(ElasticSearch,Logstash,Kibana),filebeat 初體驗

本文章參考: [Elastic stack（安裝篇) - HackMD](https://hackmd.io/@QCZ_Kvv1ScixyAPzRYDKWQ/ry2Inu5HE?type=view)
大量操作參照如上

<!--more-->

## 安裝 Elasticsearch

官方說明的安裝步驟:
[Download Elasticsearch Free • Get Started Now | Elastic](https://www.elastic.co/downloads/elasticsearch-no-jdk)
1. Download and install one of the supported JDKs for Elasticsearch
2. Download and unzip Elasticsearch
3. Run bin/elasticsearch (or bin\elasticsearch.bat on Windows)
4. Run curl http://localhost:9200/ or Invoke-RestMethod http://localhost:9200 with PowerShell

我這邊用 VM 來安裝 ubuntu 還境

[Vagrant box ubuntu/focal64 - Vagrant Cloud](https://app.vagrantup.com/ubuntu/boxes/focal64)
```bash=
vagrant init ubuntu/focal64
vagrant up
```
可能會遇到這個問題
[vagrant 啟動無法自動掛載解決方法 | 程式狂想筆記](https://malagege.github.io/blog/2020/06/26/vagrant-%E5%95%9F%E5%8B%95%E7%84%A1%E6%B3%95%E8%87%AA%E5%8B%95%E6%8E%9B%E8%BC%89%E8%A7%A3%E6%B1%BA%E6%96%B9%E6%B3%95/)

vagrant 建議 RAM 調成 3GB

VM 設一個可以連的IP

![](https://i.imgur.com/5XQeiq0.png)


### 安裝 JDK (這部分請跳過)

因為我安裝 ElasticSearch 遇到could not find java; set JAVA_HOME or ensure java is in PATH
好像是 deb 安裝才會遇到這個問題
設定 JAVA_HOME 還是無法解決

[Elastic Support Matrix | Elasticsearch](https://www.elastic.co/support/matrix#matrix_jvm)
我選 Elasticsearch 7.12.x 
JDK 對應版本選 11

```bash=
sudo apt-get install openjdk-11-jdk

# 確認版本
java --version
# openjdk 11.0.11 2021-04-20
# OpenJDK Runtime Environment (build 11.0.11+9-Ubuntu-0ubuntu2.20.04)
# OpenJDK 64-Bit Server VM (build 11.0.11+9-Ubuntu-0ubuntu2.20.04, mixed mode, sharing)
```

設定 JAVA_HOME

[java - How to set JAVA_HOME in Linux for all users - Stack Overflow](https://stackoverflow.com/questions/24641536/how-to-set-java-home-in-linux-for-all-users)

```bash= /etc/profile
# 請查看對印自己Java路徑做調整
JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
ES_JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
```

### 安裝 Elasticsearch


[Download Elasticsearch Free • Get Started Now | Elastic](https://www.elastic.co/downloads/elasticsearch-no-jdk)
選對應版本

[Install Elasticsearch with Debian Package | Elasticsearch Guide [7.12] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/7.12/deb.html#deb-repo)
```bash=
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.12.1-amd64.deb
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.12.1-amd64.deb.sha512
shasum -a 512 -c elasticsearch-7.12.1-amd64.deb.sha512 
sudo dpkg -i elasticsearch-7.12.1-amd64.deb
```

#### 安裝翻車排除
![](https://i.imgur.com/l1KX7wU.png)

[elasticsearch之JAVA环境变量报错：could not find java; set JAVA_HOME or ensure java is in PATH - 峰哥ge - 博客园](https://www.cnblogs.com/FengGeBlog/p/10266126.html)

設定JAVA_HOME 安裝也沒用
上面 deb 是函JAVA的
目前推估 安裝 deb 才會遇到這個問題

單純抓zip應該可以設定 JAVA_HOME 解決

### 啟動 ElasticSearch

```
sudo service elasticsearch start
```

確認是否成功

```bash=
curl http://localhost:9200/
# {
#   "name" : "ubuntu-focal",
#   "cluster_name" : "elasticsearch",
#   "cluster_uuid" : "AjrnvASjSG6aWrJIFhzSYg",
#   "version" : {
#     "number" : "7.12.1",
#     "build_flavor" : "default",
#     "build_type" : "deb",
#     "build_hash" : "3186837139b9c6b6d23c3200870651f10d3343b7",
#     "build_date" : "2021-04-20T20:56:39.040728659Z",
#     "build_snapshot" : false,
#     "lucene_version" : "8.8.0",
#     "minimum_wire_compatibility_version" : "6.8.0",
#     "minimum_index_compatibility_version" : "6.0.0-beta1"
#   },
#   "tagline" : "You Know, for Search"
# }
```

## 設定 ElasticSearch

預設是 localhost 連線
要外連預需要設定

/etc/elasticsearch/elasticsearch.yml

```yaml=
# By default Elasticsearch is only accessible on localhost. Set a different
# address here to expose this node on the network:
#
#network.host: 192.168.0.1
#
# By default Elasticsearch listens for HTTP traffic on the first free port it
# finds starting at 9200. Set a specific HTTP port here:
#
#http.port: 9200
```

## 安裝 Kinbana


[Install Kibana with Debian package | Kibana Guide [7.12] | Elastic](https://www.elastic.co/guide/en/kibana/7.12/deb.html#install-deb)
```bash=
wget https://artifacts.elastic.co/downloads/kibana/kibana-7.12.1-amd64.deb
shasum -a 512 kibana-7.12.1-amd64.deb 
sudo dpkg -i kibana-7.12.1-amd64.deb
```


### 設定 kibana

設定對外 IP

補充: 這邊 Vagrant 要設定 VM 對外 IP
![](https://i.imgur.com/MH2wTZ9.png)

要把 kibana 設定這個 IP

![](https://i.imgur.com/w9NM5hV.png)

#### Kibana server is not ready yet

1. 確認本機 ElasticSearch 是否啟動
2. 確認 kibana.yml 裡面 ElasticSearch 設定是否正確(預設為localhost:9200)
3. [Kibana server is not ready yet出现的原因_FV8023的博客-CSDN博客](https://blog.csdn.net/FV8023/article/details/96427702)

我後來發現 ElasticSearch 重啟的時候
沒辦法啟動，後來 RAM 調大(2GB)就正常了


## logstash 

### 安裝 logstash


```bash=
wget https://artifacts.elastic.co/downloads/logstash/logstash-7.12.1-amd64.deb
sudo apt install ./logstash-7.12.1-amd64.deb 
```

安裝時候當機，所以RAM調成 3GB
可見這個真的很吃記憶體

### Hello World

```
/usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

我這邊沒看到 Hello World
基本上就是有問題


### 翻車時間

```
Using bundled JDK: /usr/share/logstash/jdk
OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
```

[Hello World 系列 - Logstash](https://tomme.me/hello-world-series-logstash/)

後來發現不是翻車，是執行 logstash 需要一段時間(VM效能不好)
跑了一下，我看`htop` CPU 吃到100%
所以要等一下

```
Using bundled JDK: /usr/share/logstash/jdk
WARNING: Could not find logstash.yml which is typically located in $LS_HOME/config or /etc/logstash. You can specify the path using --path.settings. Continuing using the defaults
Could not find log4j2 configuration at path /usr/share/logstash/config/log4j2.properties. Using default config which logs errors to the console
[INFO ] 2021-05-01 16:04:55.718 [main] runner - Starting Logstash {"logstash.version"=>"7.12.1", "jruby.version"=>"jruby 9.2.13.0 (2.5.7) 2020-08-03 9a89c94bcc OpenJDK 64-Bit Server VM 11.0.10+9 on 11.0.10+9 +indy +jit [linux-x86_64]"}
[INFO ] 2021-05-01 16:04:55.779 [main] writabledirectory - Creating directory {:setting=>"path.queue", :path=>"/usr/share/logstash/data/queue"}
[INFO ] 2021-05-01 16:04:55.815 [main] writabledirectory - Creating directory {:setting=>"path.dead_letter_queue", :path=>"/usr/share/logstash/data/dead_letter_queue"}
[WARN ] 2021-05-01 16:04:56.611 [LogStash::Runner] multilocal - Ignoring the 'pipelines.yml' file because modules or command line options are specified
[INFO ] 2021-05-01 16:04:56.666 [LogStash::Runner] agent - No persistent UUID file found. Generating new UUID {:uuid=>"f2a71911-edf5-4c53-bf68-5a31e6ad8dd7", :path=>"/usr/share/logstash/data/uuid"}
[INFO ] 2021-05-01 16:04:58.798 [Api Webserver] agent - Successfully started Logstash API endpoint {:port=>9600}
[INFO ] 2021-05-01 16:05:00.048 [Converge PipelineAction::Create<main>] Reflections - Reflections took 101 ms to scan 1 urls, producing 23 keys and 47 values 
[INFO ] 2021-05-01 16:05:01.424 [[main]-pipeline-manager] javapipeline - Starting pipeline {:pipeline_id=>"main", "pipeline.workers"=>2, "pipeline.batch.size"=>125, "pipeline.batch.delay"=>50, "pipeline.max_inflight"=>250, "pipeline.sources"=>["config string"], :thread=>"#<Thread:0x701b0f11 run>"}
[INFO ] 2021-05-01 16:05:03.202 [[main]-pipeline-manager] javapipeline - Pipeline Java execution initialization time {"seconds"=>1.77}
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by com.jrubystdinchannel.StdinChannelLibrary$Reader (file:/usr/share/logstash/vendor/bundle/jruby/2.5.0/gems/jruby-stdin-channel-0.2.0-java/lib/jruby_stdin_channel/jruby_stdin_channel.jar) to field java.io.FilterInputStream.in
WARNING: Please consider reporting this to the maintainers of com.jrubystdinchannel.StdinChannelLibrary$Reader
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
[INFO ] 2021-05-01 16:05:03.290 [[main]-pipeline-manager] javapipeline - Pipeline started {"pipeline.id"=>"main"}
The stdin plugin is now waiting for input:
[INFO ] 2021-05-01 16:05:03.469 [Agent thread] agent - Pipelines running {:count=>1, :running_pipelines=>[:main], :non_running_pipelines=>[]}
hello
{
          "host" => "ubuntu-focal",
       "message" => "hello",
      "@version" => "1",
    "@timestamp" => 2021-05-01T16:05:55.327Z
}
```



### 設定

1. logstash 預設會先抓/etc/logstash/conf.d 的config檔案
2. logstash 預設會先抓/etc/logstash/logstash.yml

[2.1 Config Of Logstash · ELK Stack 教學](https://mmx362003.gitbooks.io/elk-stack-guide/content/config_of_logstash.html)



### config 設定


#### stdin 

[标准输入(Stdin) | Logstash 最佳实践](https://doc.yonyoucloud.com/doc/logstash-best-practice-cn/input/stdin.html)

```
input {
    stdin {
        add_field => {"key" => "value"}
        codec => "plain"
        tags => ["add"]
        type => "std"
    }
}


output{
    stdout{}
}
```

```bash=
# 在/usr/share/logstash 執行，stdin.conf 放在這邊執行
sudo bin/logstash -f stdin.conf
# {
#           "tags" => [
#         [0] "add"
#     ],
#           "host" => "ubuntu-focal",
#           "type" => "std",
#            "key" => "value",
#       "@version" => "1",
#        "message" => ":q",
#     "@timestamp" => 2021-05-01T16:38:27.958Z
# }
```


#### file log

```
input{
    file {
        path => ["/var/log/*.log", "/var/log/message"]
        type => "system"
        start_position => "beginning"
    }
}


output{
    stdout{}
}
~                    
```

放在 /etc/logstash/conf/ 裡面
執行 logstash 就能被觸發

其他的就不記了
可參考下面網址
[输入插件(Input) | Logstash 最佳实践](https://doc.yonyoucloud.com/doc/logstash-best-practice-cn/input/index.html)

[GitHub - chenryn/logstash-best-practice-cn: 本书已出版《ELK Stack权威指南》](https://github.com/chenryn/logstash-best-practice-cn)

[GitHub - chenryn/ELKstack-guide-cn: ELK Stack 中文指南](https://github.com/chenryn/ELKstack-guide-cn)


#### output

```
output {
    stdout {
        codec => rubydebug
        workers => 2
    }
}
```

#### output file 

```
output {
    file {
        path => "/path/to/%{+yyyy/MM/dd/HH}/%{host}.log.gz"
        message_format => "%{message}"
        gzip => true
    }
}

````

#### output Elasticsearch

```
output {
    elasticsearch {
        host => "192.168.0.2"
        protocol => "http"
        index => "logstash-%{type}-%{+YYYY.MM.dd}"
        index_type => "%{type}"
        workers => 5
        template_overwrite => true
    }
}
```

## filebeat

### 安裝 filebeat

```bash=
wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.12.1-amd64.deb
sudo apt install ./filebeat-7.12.1-amd64.deb
```

### 設定檔

/etc/filebeat/filebeat.yml
裡面有很多 input 都disable
output預設連到 localhost:9200 的 elasticsearch
假如想用到 logstash 需要手動註解調，並打開logstash output 那邊

![](https://i.imgur.com/2AufteG.png)

![](https://i.imgur.com/YdOZW1G.png)

請依照需求調整

```bash=
# 可以看到測試 log
filebeat -e
# 沒問題的話，service 啟動
sudo service filebeat start
```

### 相關模組

```bash=
# 查看可以用的模組
filebeat modules list
```

這邊先不深入研究
[一篇文章搞懂filebeat（ELK） - 一寸HUI - 博客园](https://www.cnblogs.com/zsql/p/13137833.html)


[GitHub - revolunet/traefik-filebeats-kibana: sample docker-compose with traefik and kibana dashboard using filebeats](https://github.com/revolunet/traefik-filebeats-kibana)


### logstash 和 filebeat 關係

網路上看到 filebeat 都傳送到 logstash 教學比較多
但後來實作發現不是這樣
可以依照需求直接傳到 elasticsearch
甚至可以做到 LoadBalance 傳到 logstash
詳細可以看:[搞懂ELK並不是一件特別難的事（ELK） - ⎝⎛CodingNote.cc ⎞⎠](https://codingnote.cc/zh-tw/p/144718/)

甚是中間可以搭 Message Queue

### kibana 設定filebeat

去管理設定 Index Mangament
點選 Create Index Partten
step 1 打 filebeat*
step 2 選 @timestamp 
新增完成，取選 Discover
就可以用 KQL 查詢

不方便截圖，所以這裡就簡單紀錄一下

## 題外話

原本目標， Nginx 抓出 Acess Log 做地圖分析
不過想說安裝細結清楚一點比較好
沒想到就花了不少時間整理

目前查看我這個抓 Access Log GeoIP 方法

1. 從 Nginx 下手取 geoip資訊(經緯度、縣市名稱)

[Total Nginx monitoring, with application performance and a bit more, using Telegraf/InfluxDB/Grafana. | by Alexey Nizhegolenko | FAUN](https://faun.pub/total-nginx-monitoring-with-application-performance-and-a-bit-more-using-8fc6d731051b)

2. Logstash Filter GeoIP 模組取得資訊

[Grafana展示精美的nginx訪問日誌圖表_小小________ - MdEditor](https://www.mdeditor.tw/pl/g4XZ/zh-tw)

Loki 目前沒有取出來 GeoIP 方法
[Add support for Geo IP · Issue #2120 · grafana/loki · GitHub](https://github.com/grafana/loki/issues/2120)
其實 Logstash 也能跟 Loki 串
[Logstash | Grafana Labs](https://grafana.com/docs/loki/latest/clients/logstash/)
但說真的 Logstash 效能感覺也不是很好(上面測試跑都要花一點時間，我還以為跑失敗)
在樹梅派跑可能也很吃力

本來想用 docker 執行看看，但官方沒有做 arm 版本
就不測試了
```bash=
docker run -it  --rm logstash:7.12.1 logstash -e 'input { stdin { } } output { stdout {} }' 
```

GeoIP 哪貼心血來潮再進行測試
這部分也沒有現成包好 docker 可以直接玩

## 其他不錯文章

[ElasticSearch 全文检索技术（一） - 简书](https://www.jianshu.com/p/f57176a3c03f)

[logstash 和filebeat 是什么关系？ - 知乎](https://www.zhihu.com/question/54058964)

[MongoDB和Elasticsearch的对比 | Leriou's Tavern](https://leriou.github.io/2019-01-09-mongodb-compareto-elasticsearch/)

[搞懂ELK並不是一件特別難的事（ELK） - ⎝⎛CodingNote.cc ⎞⎠](https://codingnote.cc/zh-tw/p/144718/)

[关于kibana的可视化可能都在这篇文章里了 - SegmentFault 思否](https://segmentfault.com/a/1190000022533196)

[Kibana7.9.2设置elasticsearch索引过期时间，到期自动删除_螺蛳粉不加葱的博客-CSDN博客_es设置索引过期时间](https://blog.csdn.net/qq_41631365/article/details/109773675)

[[Elasticsearch] 基本概念 & 搜尋入門 | 小信豬的原始部落](https://godleon.github.io/blog/Elasticsearch/Elasticsearch-getting-started/)