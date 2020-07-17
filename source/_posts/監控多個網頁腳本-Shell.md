---
title: 監控多個網頁腳本(Shell)
date: 2020-07-15 23:35:56
tags: [shell,web]
categories: Linux
---

最近專案 Server 特別多...
所以在想有什麼腳本可以快速確認服務正常
又爬文整理一下
主要是參考這篇 [6 Methods to Quickly Check if a Website is up or down from the Linux Terminal | 2daygeek.com](https://www.2daygeek.com/linux-command-check-website-is-up-down-alive/)

<!--more-->

主要參考
```bash
# vi curl-url-check-1.sh

#!/bin/bash
for site in www.google.com google.co.in www.xyzzz.com
do
if curl -I "$site" 2>&1 | grep -w "200\|301" ; then
    echo "$site is up"
else
    echo "$site is down"
fi
echo "----------------------------------"
done
```

最後只需要`# sh curl-url-check-1.sh`
就能測試了

## 我簡單修改

```bash
#!/bin/bash

url_list=(
# web
https://***.jsp
https://***.jsp
https://***.jsp
https://***.jsp
https://***.jsp
http://1***.jsp
http://1***.jsp
# api
https://***.jsp
https://***.jsp
https://***.jsp
https://***.jsp
https://***.jsp
http://1***.jsp
http://1***.jsp
)

for site in "${url_list[@]}"
do
if curl -k -i "$site" 2>&1 | grep -w "200\|301" ; then
    echo "$site is up"
else
    echo "$site is down"
fi
echo "----------------------------------"
done
```

## 加入顏色

太多了，眼睛看的不是很舒服

```bash
#!/bin/bash

COLOR_REST='\e[0m';
COLOR_GREEN='\e[0;32m';
COLOR_RED='\e[0;31m';

url_list=(
# WEB
https://***.jsp
https://***.jsp
https://***.jsp
https://***.jsp
http://1***.jsp
http://1***.jsp
# API
https://***.jsp
https://***.jsp
https://***.jsp
https://***.jsp
http://1***.jsp
http://1***.jsp
)

for site in "${url_list[@]}"
do
if curl -k -i "$site" 2>&1 | grep -w "200\|301" ; then
    echo -e "$site is ${COLOR_GREEN}up${COLOR_REST}"
else
    echo -e "$site is ${COLOR_RED}down${COLOR_REST}"
fi
echo "----------------------------------"
done
```


完成，愉悅 XD

[6 Methods to Quickly Check if a Website is up or down from the Linux Terminal | 2daygeek.com](https://www.2daygeek.com/linux-command-check-website-is-up-down-alive/)
裡面還有fping 
但好像無法對單一網址
所以就沒繼續研究