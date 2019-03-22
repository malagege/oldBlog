---
title: 簡單使用shell做個硬碟空間警告discord通知
date: 2019-03-21 20:29:50
tags: [linux]
categories: Linux
---

最近研究出來 discord webhook
想說讓我的動物機玩玩
但是動物機只有裝 python
也沒看到 monolog for discord
~~不想自幹~~因為我不熟 python
原本想說使用看看 nodejs
不過想說用原生 shell 看看
不用安裝環境

<!--more-->

隨便 google 爬了一個範例[Shell script to watch the disk space - nixCraft](https://www.cyberciti.biz/tips/shell-script-to-watch-the-disk-space.html)
因為有語系關係，所以有調整程式碼

> 踩雷注意!!!
> xxx(string): not found
> shell =前後盡量不要有空白

```sh
#!/bin/sh
df -H | grep -vE '^Filesystem|tmpfs|cdrom|udev|已用' | awk '{ print $5 " " $1 }' | while read output;
do
  echo $output
  usep=$(echo $output | awk '{ print $1}' | cut -d'%' -f1  )
  partition=$(echo $output | awk '{ print $2 }' )
  if [ $usep -ge 80 ]; then
    msg="注意快超出硬碟空間\\\"$partition\\\" ($usep%) on $(hostname) as on $(date)"
    url='https://discordapp.com/api/webhooks/*'
    curl -H "Content-Type: application/json" \
    -X POST \
    -d "{\"username\": \"system\", \"content\": \"${msg}\"}" $url
  fi
done
```

剩下就是設定一下排程

```sh
0 18 * * * sh /root/notify/disk.sh > /dev/null 2>&1
```

任務完成!!

備注:
Discord Webhook 可以參考這篇下面[使用 monolog 和 slack 做 webhook 小記 | 程式狂想筆記](https://malagege.github.io/blog/2019/03/19/%E4%BD%BF%E7%94%A8monolog%E5%92%8Cslack-%E5%81%9Awebhook%E5%B0%8F%E8%A8%98/)
記得上面 code 要把網址換掉
