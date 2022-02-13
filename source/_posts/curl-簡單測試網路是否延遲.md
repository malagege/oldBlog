---
title: curl 簡單測試網路是否延遲
date: 2022-02-13 23:08:24
tags: [curl]
categories: 網路
---
因為最近 Line 測試 API，有少數情況會 connection timed out，所以簡單測試是不是打 API 會不會 timedout ，測試發現會失敗。

<!--more-->

## 方法一 (我使用這個)
```bash=
for i in `seq 100`; do curl -o /dev/null -s -w %{http_code}:%{time_total} http://www.baidu.com;echo " "; done 
```

參考：[使用shell 脚本做简单的压力测试_ISaiSai的专栏-CSDN博客_shell压力测试脚本](https://blog.csdn.net/ISaiSai/article/details/52303592)


## 方法二

參考：[shell script web 壓力測試](https://www.puritys.me/docs-blog/article-452-shell-script-web-%E5%A3%93%E5%8A%9B%E6%B8%AC%E8%A9%A6.html)


```bash=
    for i in {1..30}
    do
        s="$s\n$i"          
    done    
     
    echo -e $s | xargs  -n 1 -P8 -I% curl  -w "@curl-format.txt"  -I -k "https://www.yourhost.com/path?offset=0&limit=10" --max-time 30  2>&1
```



curl-format.txt

```
    http_code:  %{http_code}\n                                                                                                        
        time_namelookup:  %{time_namelookup}\n
           time_connect:  %{time_connect}\n
        time_appconnect:  %{time_appconnect}\n
       time_pretransfer:  %{time_pretransfer}\n
          time_redirect:  %{time_redirect}\n
     time_starttransfer:  %{time_starttransfer}\n
                        ----------\n
             time_total:  %{time_total}\n
```

我本來找有沒有類似 TLS 相關解析測試，不過沒看到。想找類似這樣
![](https://i.imgur.com/BHTkCH0.png)

