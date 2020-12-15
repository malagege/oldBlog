---
title: Docker 使用容量撐爆 Raspberry PI 硬碟空間
date: 2020-11-25 19:53:11
tags: [docker]
categories: Docker
---

使用 Docker 我們知道rm container 所有東西不會被保留
但我往往會忽略有 images 占空間
我發現 PI 1 已經吃 14GB (總共空間 15GB)
後來發現是裝太多 images
原因是我往往先丟 PI 1 測試能不能跑
日積月累爆掉
PS: 是更新 apt upgrade 才發現   哈哈

<!--more-->

## 尋找所占目錄空間方法

參考: [Linux - 找出佔用磁碟空間最大的目錄/檔案](http://blog.ilc.edu.tw/blog/index.php?op=printView&articleId=581867&blogId=25793)

```sh
du -hsx /home/* | sort -hr | head
```

這個好處能找到外面層所占目錄
可以慢慢跑裡面哪一個資料夾吃大空間

最後改成當下執行目錄前大空間大小

```sh
du -hsx ./* | sort -hr | head
```

找到 docker

其實 df 就能看到是 /lib/docker/overlay/...

## docker 清除空間

以下參考整理
- [Docker:我們的硬盤空間去哪了？ - 知乎](https://zhuanlan.zhihu.com/p/36956285)
- [Docker 空間使用分析與清理-阿里雲開發者社區](https://developer.aliyun.com/article/272173)
- [如何清理Docker占用的磁盘空间? | Fundebug博客 - 一行代码搞定BUG监控 - 网站错误监控|JS错误监控|资源加载错误|网络请求错误|小程序错误监控|Java异常监控|监控报警|Source Map|用户行为|可视化重现](https://blog.fundebug.com/2018/01/10/how-to-clean-docker-disk/)

### 確認所占空間


```sh
docker system df

Images              22                  6                   4.827GB             3.517GB (72%)
Containers          6                   4                   93.54MB             8.661MB (9%)
Local Volumes       3                   1                   214.6kB             66.34kB (30%)
Build Cache         0                   0                   0B                  0B

# 詳細資訊
docker system df -v
# 可以看更詳細資訊...
```


### 手動清除

```sh
# 刪除所有關閉的容器

docker ps -a | grep Exit | cut -d ' ' -f 1 | xargs docker rm

# 刪除所有 dangling 鏡像(即無 tag 的鏡像)：

docker rmi $(docker images | grep "^<none>" | awk "{print $3}")

# 刪除所有 dangling 數據卷(即無用的 volume)：

docker volume rm $(docker volume ls -qf dangling=true)
```

這邊有更快清除方法

```
docker system prune
```



>    Tips ：

>    不同狀態的鏡像

>        已使用鏡像（used image）： 指所有已被容器（包括已停止的）關聯的鏡像。即 docker ps -a 看到的所有容器使用的鏡像。
>        未引用鏡像（unreferenced image）：沒有被分配或使用在容器中的鏡像，但它有 Tag 信息。
>        懸空鏡像（dangling image）：未配置任何 Tag （也就無法被引用）的鏡像，所以懸空。這通常是由於鏡像 build 的時候沒有指定 -t 參數配置 Tag 導致的。比如:

>REPOSITORY                                                   TAG                 IMAGE ID            CREATED             SIZE
> < none >                                                      <none>              6ad733544a63        5 days ago          1.13 MB   > # 懸空鏡像（dangling image）
>    掛起的卷（dangling Volume)
>    類似的，dangling=true 的 Volume 表示沒有被任何容器引用的卷。

docker system prune 自動清理說明：

該指令默認會清除所有如下資源：
    已停止的容器（container）
    未被任何容器所使用的卷（volume）
    未被任何容器所關聯的網絡（network）
    所有懸空鏡像（image）。
該指令默認只會清除懸空鏡像，未被使用的鏡像不會被刪除。
添加 -a 或 --all 參數後，可以一併清除所有未使用的鏡像和懸空鏡像。
可以添加 -f 或 --force 參數用以忽略相關告警確認信息。
指令結尾處會顯示總計清理釋放的空間大小。



以上指令沒有清除到空間，但加了 -a 就清除掉了
~~可以做 apt upgrade 了~~
```
docker system prune -a
```

有空再整理什麼是 懸空鏡像 東西... 跟 docker log 處理