---
title: Systemd 時間排程筆記
date: 2020-12-07 23:01:20
tags: [systemd,crontab]
categories: Linux
---



一般我們使用排程都是用 crontab
再搭配使用flock 去做程式執行管控，這一段也有可能時間寫死程式一個flag，這無疑是要佔用檔案了
前幾天我看到 systemd 可以做排程設定
所以就簡單研究一下

其實之前就有簡單研究
一直覺得這個東西非常實用
用配置就能完成程式複雜判斷

網路上還滿多相關教學
應該可以用在正式環境上面

<!--more-->

## 了解工具名稱

Systemd 雖然指令叫 `systemctl`，但網路上看到都叫 systemd。

> 查看和控制systemd的主要命令是systemctl。該命令可用查看系統狀態和管理系統及服務。詳見systemctl(1)。 

參考: [systemd (正體中文) - ArchWiki](https://wiki.archlinux.org/index.php/Systemd_(%E6%AD%A3%E9%AB%94%E4%B8%AD%E6%96%87))


## 簡單使用

之前[rclone 掛載硬碟小記 | 程式狂想筆記](https://malagege.github.io/blog/2019/10/14/rclone-%E6%8E%9B%E8%BC%89%E7%A1%AC%E7%A2%9F%E5%B0%8F%E8%A8%98/#%E8%87%AA%E5%8B%95%E6%8E%9B%E8%BC%89)有簡單用過

後面記得要加`sudo systemctl daemon-reload`
後面記得要加`sudo systemctl daemon-reload`
後面記得要加`sudo systemctl daemon-reload`

因為很重要，所以講三遍!!!
一不注意就忘記了XD

### 一般 root 使用小記

.target
### UNIT,Service,Install

> [Unit] 小節選項

> 單元文件中的 [Unit] 小節 包含與單元類型無關的通用信息。 可用的選項(亦稱"指令"或"屬性")如下：

參考: [systemd.unit 中文手册 [金步国]](http://www.jinbuguo.com/systemd/systemd.unit.html)

詳細內容:[Systemd 入門教程：實戰篇 - 阮一峰的網絡日誌](https://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-part-two.html) {% asset_link web1.png 備份圖 %}

感覺上面重點都提到了...
~~我不忍心完整 copy & paste~~沒大握整理好，可以到裡面看!!
這邊整理我覺得之後可能會用到的

看大多數接學都是用 root 設定 systemd
**前陣子有看到使用一般 user 操作 systemd**
本篇重點實作網路案例操作看看


## systemctl --user

最近爬到一篇 一般user也可以使用
一般主機排程都是設定在 root(我之前工作大多數都 這樣)
但最近在想是不是應該可以切開一個全縣讓工程師去做設定
畢竟大多數 MIS 也看不懂程式...

### 一般使用者實作

建置一個 Hello-world.service 

```bash
vim ~/.config/systemd/user/hello-world.service 
```

```ini hello-wrold.service
[Unit]
Description=Hello World Service
After=systend-user-sessions.service

[Service]
Type=simple
ExecStart=/home/malagege/hello-world.sh
SyslogIdentifier=HelloWorldService

[Install]
WantedBy=default.target
````

啟用 hello-world.service
```bash
systemctl --user  enable  hello-world.service                                                                                   
Created symlink /home/malagege/.config/systemd/user/default.target.wants/hello-world.service → /home/malagege/.config/systemd/user/hello-world.service.
```

~~竟然不用做 systemctl daemon-reload!!! ~~
~~**我還實有看到有的教學有用**`systemctl --user daemon-reload`~~
修改 timer 的時候需要 `systemctl --user daemon-reload` 才會生效

但我這邊沒使用就能用了
沒出現先enable 就會有了

```
 systemctl --user  status  hello-world.service
● hello-world.service - Hello World Service
   Loaded: loaded (/home/malagege/.config/systemd/user/hello-world.service; enabled; vendor preset: enabled)
   Active: inactive (dead)
systemctl --user  start  hello-world.service
 ~/.config/systemd/user 
systemctl --user  status  hello-world.service
● hello-world.service - Hello World Service
   Loaded: loaded (/home/malagege/.config/systemd/user/hello-world.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2020-11-17 23:26:23 CST; 4s ago
 Main PID: 10483 (hello-world.sh)
   CGroup: /user.slice/user-1000.slice/user@1000.service/hello-world.service
           ├─10483 /bin/bash /home/malagege/hello-world.sh
           └─10485 sleep 30

11月 17 23:26:23 malagege-K42JB systemd[1317]: Started Hello World Service.
```

/home/malagege/.config/systemd/user/default.target.wants 裡面會出現  hello-world.service

systemctl --user  disable  hello-world.service

成功完成簡單操作

#### 上面案例執行參數小記

```
# 標準輸出到
StandardOutput=syslog
# 標準錯誤輸出到
StandardError=syslog
# 設置syslog中log的程序名稱
SyslogIdentifier=helloexample
# 設置syslog中log類型
SyslogFacility=local0
# 設置syslog中log級別，此處為info
SyslogLevel=info
```
Install 需要設定
這邊是網路參考去配置的

參考:  [[Linux] 利用 Systemd 管理應用程式 – 常用指令 | 從入門到放棄](https://exfast.me/2018/11/linux-use-systemd-manage-application-common-instructions/)

## timer 

簡單說一下，這邊操作 systemd 的 timer 只有在 user 登入時候才會跑
所以需要做到開機跑 timer，需要設定 Linux 配置
**我這邊有測試桌面登入，登出會有問題，但不確定是不是每個 Linux 就會這樣，但 Command Line 模式都很正常**
下面會提到

### 初始設定

```bash
loginctl show-user `__user_id__`
```

要設定

```
loginctl enable-linger `id -u` # 後面是放使用者名稱 或 id
```

會出現 

```

...
...
...
Linger=no
```

`Linger=no `代表沒有啟用
一般通常不會打開
這個不打開的話
只要登出使用者帳號，當下 systemd 的 timer 都不會執行
~~**這邊要注意!!疑似要用 root 權限設定**~~
~~像我用一般使用者設定~~
~~但登出 timer 就沒有繼續跑了...~~
~~後來嘗試很就，root 設定 loginctl 就成功了~~
結果後來桌面登出有時候 systemctl --user 還是會中斷...

> 警告： systemd 服務是 沒有 會話的， 它們在 logind 狀態之外運行， 所以不要在 lingering 中啟用自動登陸的功能，這會導致 會話中斷。

參考:[systemd (簡體中文)/User (簡體中文) - ArchWiki](https://wiki.archlinux.org/index.php/Systemd_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)/User_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E9%9A%8F%E7%B3%BB%E7%BB%9F%E8%87%AA%E5%8A%A8%E5%90%AF%E5%8A%A8systemd%E7%94%A8%E6%88%B7%E5%AE%9E%E4%BE%8B)

用別的使用者下`ps -fu _user_`就可以看到有沒有起 `systemd --user`

有很大機率是這個原因...，不過不知道是我的 ElementaryOS 關係
顯示自動開啟不是很正確，有時候桌面登出還是會中斷 systemd 
有空我再用樹梅派實體機器測試...

**目前猜測用桌面登出會中斷 systemd**
PS: 我使用的是 ElementaryOS

- [settings - How to enable automatic login after installing elementary OS Loki - elementary OS Stack Exchange](https://elementaryos.stackexchange.com/questions/7726/how-to-enable-automatic-login-after-installing-elementary-os-loki)
- [systemd - How to apply lingering immedeately - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/462845/how-to-apply-lingering-immedeately)
- [用 systemd 管理 laravel 队列和定时任务 - fairycat](https://blog.fairycat.cn/post/128)

## 操作紀錄

參考: [systemd timer：取代cron和at的定时任务 | 骏马金龙](https://www.junmajinlong.com/linux/systemd/systemd_timer/#systemd-timer%E5%85%A5%E9%97%A8%E7%A4%BA%E4%BE%8B%EF%BC%9A%E6%AF%8F3%E7%A7%92%E8%BF%90%E8%A1%8C%E4%B8%80%E6%AC%A1)


```ini hello-timer.service
[Unit]
Description = "Hello Timer Description"

[Service]
ExecStart = /home/malagege/hello-timer.sh
# ExecStart = /usr/bin/curl -s -o /dev/null -w '%{http_code}' 'https://www.junmajinlong.com'
```

```ini hello-timer.timer
[Unit]
Description = "run hello timer  every 3 seconds"

[Timer]
OnActiveSec = 1s
OnUnitInactiveSec = 3s
AccuracySec = 1us
RandomizedDelaySec = 0

[Install]
WantedBy = timers.target

```

```
systemctl --user  start  hello-timer.timer
```

```
systemctl --user list-timers --all
NEXT                         LEFT    LAST                         PASSED UNIT              ACTIVATES
Wed 2020-11-18 00:05:38 CST  1s left Wed 2020-11-18 00:05:35 CST  1s ago hello-timer.timer hello-timer.service
```


```
systemctl --user status  hello-timer.timer
● hello-timer.timer - "run hello timer  every 3 seconds"
   Loaded: loaded (/home/malagege/.config/systemd/user/hello-timer.timer; enabled; vendor preset: enabled)
   Active: active (waiting) since Wed 2020-11-18 00:03:25 CST; 9min ago
  Trigger: Wed 2020-11-18 00:13:15 CST; 825ms left

11月 18 00:03:25 malagege-K42JB systemd[1317]: Started "run hello timer  every 3 seconds".
 ~/.config/systemd/user 
systemctl --user status  hello-timer.service
● hello-timer.service - "Hello Timer Description"
   Loaded: loaded (/home/malagege/.config/systemd/user/hello-timer.service; static; vendor preset: enabled)
   Active: inactive (dead) since Wed 2020-11-18 00:13:18 CST; 2s ago
  Process: 20922 ExecStart=/home/malagege/hello-timer.sh (code=exited, status=0/SUCCESS)
 Main PID: 20922 (code=exited, status=0/SUCCESS)

11月 18 00:13:18 malagege-K42JB systemd[1317]: Started "Hello Timer Description".
11月 18 00:13:18 malagege-K42JB hello-timer.sh[20922]: hello world
```

很有趣的是 hello-timer.service 裡面狀態是static 
不太確定是 .timer 影響到他


查看 log 

```
journalctl --user -u hello-timer.service
```

### 動態延遲執行

```
# 定時器啟動後，再過10分鐘第一次觸發定時任務
OnActiveSec=10m
# 每次執行完任務後，再過15分鐘後再次觸發定時任務
OnUnitInactiveSec=15m
# 觸發事件後，允許推遲0-10分鐘再執行被觸發的任務
AccuracySec=10m
```

> 以上指令的效果是：
> 
>  -   啟動定時器後的10m-20m內的任一時間點觸發第一次定時任務
>  -  之後每隔15m-25m再次觸發定時任務 


作者: 骏马金龙
链接: https://www.junmajinlong.com/linux/systemd/systemd_timer/
来源: 骏马金龙
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


分開看指令，可能不是很了解
OnUnitInactiveSec 排程執行完之後會執行 service
AccuracySec 會增加起排程時間
所以才會變成 15~25 分鐘執行

> AccuracySec的默認值為1分鐘，所以如果不定義AccuracySec的話，即使用戶期待的是每秒觸發一次定時任務，但事實卻是會在1s-61s時間段內的一個隨機時間點觸發一次定時任務。(可以自己去觀察一下定時任務執行情況)
> 
> 但是，觸發定時任務的時間點並不表示這是執行任務的時間點。觸發了定時任務，還需要根據RandomizedDelaySec的值來決定何時執行定時任務。
> 
> RandomizedDelaySec指定觸發定時任務後還需延遲一個指定範圍內的隨機時長才執行任務。該指令默認值為0，表示觸發後立即執行任務。
> 
> 使用RandomizedDelaySec，主要是為了在一個時間範圍內分散大量被同時觸發的定時任務，從而避免這些定時任務集中在同一時間點執行而CPU爭搶。
> 
> 可見，AccuracySec和RandomizedDelaySec的目的是相反的：
> 
>     前者讓指定範圍內的定時器集中在同一個時間點一次性觸發它們的定時任務
>     後者讓觸發的、將要被執行的任務均勻分散在一個時間段範圍內
> 
> 根據以上描述，如果用戶想要讓定時任務非常精確度地執行，需要將它們設置的足夠小。例如：
> 
> ```
> AccuracySec = 1ms        # 定時器到點就觸發定時任務
> RandomizedDelaySec = 0   # 定時任務一觸發就立刻執行任務
> ```

我的感覺選 AccuracySec 應該會比較好

### 運行級別(別打錯)


[systemd時代的運行級別 | 駿馬金龍](https://www.junmajinlong.com/linux/systemd/runlevel/)

總之設定 Install 的 target 
timers.target 設定成timer.target
開機進然不會啟動
後來發現少加了s 


### 查看systemd排程時間點

```
systemctl list-timers --no-pager
```

> NEXT表示下一次要觸發定時任務的時間點
> LEFT表示現在距離下次執行任務還剩多長時間(已經確定了下一次執行的時間點)，或者顯示最近一次執行任務已經過去了多長時間(還不確定下一次執行的時間點)> ，稍後解釋了AccuracySec和RandomizedDelaySec就知道為什麼會有這兩種表示方式
> LAST表示上次觸發定時任務的時間點
> PASSED表示距離上一次執行任務已經過去多久
> UNIT表示是哪個定時器
> ACTIVATES表示該定時器所觸發的任務 

### 查看日誌

```
# 查看指定服務的所有journal日誌信息
# xxx.service是定時任務的名稱
journalctl -u xxx.service

# 實時監控尾部日誌，類似tail -f
journalctl -f -u xxx.service

# 顯示指定時間段內的日誌
# --since：從指定時間點內開始的日誌
# --until：到指定時間點為止的日誌
journalctl -u xxx.service --since="2020-07-04 19:06:23"
journalctl -u xxx.service --since="60s ago"
journalctl -u xxx.service --since="1min ago"
journalctl -u page_test.service --since="-60s"
```



### Calendar

#### 一般時間執行

全部參考:[systemd timer：取代cron和at的定时任务 | 骏马金龙](https://www.junmajinlong.com/linux/systemd/systemd_timer/#%E6%9B%B4%E4%B8%BA%E7%81%B5%E6%B4%BB%E7%9A%84%E5%AE%9A%E6%97%B6%E8%A7%84%E5%88%99%EF%BC%9AOnCalendar)
裡面內容豐富，建議看裡面內文

這邊貼範例
看範例修改就真的很方便

```
# 假如今日是2012-11-23
  Fri 2012-11-23 11:12:13 → Fri 2012-11-23 11:12:13
      2012-11-23 11:12:13 → Fri 2012-11-23 11:12:13
  2012-11-23 11:12:13 UTC → Fri 2012-11-23 19:12:13
               2012-11-23 → Fri 2012-11-23 00:00:00
                 12-11-23 → Fri 2012-11-23 00:00:00
                 11:12:13 → Fri 2012-11-23 11:12:13
                    11:12 → Fri 2012-11-23 11:12:00
                      now → Fri 2012-11-23 18:15:22
                    today → Fri 2012-11-23 00:00:00
                today UTC → Fri 2012-11-23 16:00:00
                yesterday → Fri 2012-11-22 00:00:00
                 tomorrow → Fri 2012-11-24 00:00:00
                 +3h30min → Fri 2012-11-23 21:45:22
                      -5s → Fri 2012-11-23 18:15:17
                11min ago → Fri 2012-11-23 18:04:22
      2 months 5 days ago
              @1395716396 → Tue 2014-03-25 03:59:56
```

進階使用
```
Sat,Mon..Wed   -> 周一、二、三、四
*:00           -> 每小时，省略秒位
*:*            -> 每分钟，省略秒位
*:*:*          -> 每秒
*-*-01 00:00   -> 每月一号，省略秒位
*-02~03        -> 表示2月的倒数第三天  
*-05~07/1      -> 表示5月最后7天中的每一天
*-05~1..7      -> 同上
Mon *-05~07/1  -> 表示5月最后一个星期一
*:*:3.33/10.05 -> 小数表示秒。3.33,13.38,23.43,33.48,43.53
9..17/2:00     -> 从上午9点开始每隔两小时，直到下午5点
12..14:10,20   -> 12到14点的第10分钟和第20分钟，即1[234]:[12]0:00
```

連月底跟09~17每隔兩小時執行都可以做到

```
Sat,Mon..Wed   -> 週一、二、三、四
*:00           -> 每小時，省略秒位
*:*            -> 每分鐘，省略秒位
*:*:*          -> 每秒
*-*-01 00:00   -> 每月一號，省略秒位
*-02~03        -> 表示2月的倒數第三天  
*-05~07/1      -> 表示5月最後7天中的每一天
*-05~1..7      -> 同上
Mon *-05~07/1  -> 表示5月最後一個星期一
*:*:3.33/10.05 -> 小數表示秒。3.33,13.38,23.43,33.48,43.53
9..17/2:00     -> 從上午9點開始每隔兩小時，直到下午5點
12..14:10,20   -> 12到14點的第10分鐘和第20分鐘，即1[234]:[12]0:00

```


```
Sat,Mon..Wed   -> 週一、二、三、四
*:00           -> 每小時，省略秒位
*:*            -> 每分鐘，省略秒位
*:*:*          -> 每秒
*-*-01 00:00   -> 每月一號，省略秒位
*-02~03        -> 表示2月的倒數第三天  
*-05~07/1      -> 表示5月最後7天中的每一天
*-05~1..7      -> 同上
Mon *-05~07/1  -> 表示5月最後一個星期一
*:*:3.33/10.05 -> 小數表示秒。3.33,13.38,23.43,33.48,43.53
9..17/2:00     -> 從上午9點開始每隔兩小時，直到下午5點
12..14:10,20   -> 12到14點的第10分鐘和第20分鐘，即1[234]:[12]0:00
```
##### 快速分析工具

```
systemd-analyze calendar "*:*:00"  
 Original form: *:*:00
Normalized form: *-*-* *:*:00
    Next elapse: Sat 2020-11-21 00:01:00 CST
       (in UTC): Fri 2020-11-20 16:01:00 UTC
       From now: 24s left
```
後面放時間，可以看到內容

#### 月底執行

在還沒整理上面，有特別先找的

之前就有記過[排程 crontab 時間設定月底執行方法 | 程式狂想筆記](https://malagege.github.io/blog/2019/08/04/%E6%8E%92%E7%A8%8B-crontab-%E6%99%82%E9%96%93%E8%A8%AD%E5%AE%9A%E6%9C%88%E5%BA%95%E5%9F%B7%E8%A1%8C%E6%96%B9%E6%B3%95/)
在想這個有沒有比較優的方法

```
As of version 233, systemd supports using "~" in its calendar syntax to specify dates relative to the end of the month.

OnCalendar=*-02~03 means the third last day in February (the 26th or 27th, depending on whether or not it's a leap year)

Mon *-05~07/1 and Mon *-05~01..07 are synonyms for the last Monday in May.

https://github.com/systemd/systemd/blob/v233/NEWS#L174

```
[How to configure Systemd Timer to run the service on last day of the month - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/306336/how-to-configure-systemd-timer-to-run-the-service-on-last-day-of-the-month)


#### 持久化

**Timer**段中還可以使用Unit指令和Persistent指令。

- Unit=xxx.service：默認情況下，a.timer對應要執行的任務文件是a.service，使用Unit指令可以明確指定觸發定時任務事件時要執行的文件
- Persistent=yes/no：只在使用了OnCalendar時有效，默認值no。設置yes時，會將上次執行任務的時間點保存在磁盤上，使得定時器再次被啟動時，可以立即判斷是否要執行丟失的任務 

有間單測試，我以為設定`*:*:00` 超過三分鐘會執行三次，但是不會...

timer

```ini 
[Unit]
Description = user login timer

[Timer]
AccuracySec = 1ms
RandomizedDelaySec = 0
OnCalendar = *:*:00
Persistent = yes

[Install]
WantedBy = timers.target
```
service

```
[Unit]
Description = current time when task is been executed

[Service]
ExecStart = /bin/bash -c 'date +"%%T" >>/home/malagege/a.log'
```


## socket 觸發啟動

[Node 应用的 Systemd 启动 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2016/03/node-systemd-tutorial.html)

這裡面有介紹
還覺得滿有趣的
但目前我這邊應該用不到這個功能

## 搜尋相關文章

### 詳細教學文章

* [systemd (正體中文) - ArchWiki](https://wiki.archlinux.org/index.php/Systemd_(%E6%AD%A3%E9%AB%94%E4%B8%AD%E6%96%87))
* [使用 systemd.timer 代替 crontab | C瓜農場](https://cfarm.blog.aznc.cc/%E4%BD%BF%E7%94%A8-systemd-timer-%E4%BB%A3%E6%9B%BF-crontab/)
* [使用 systemd timer 替换 crontab | Yiran's Blog](https://zdyxry.github.io/2018/06/28/%E4%BD%BF%E7%94%A8-systemd-timer-%E6%9B%BF%E6%8D%A2-crontab/)
* [systemd時代的運行級別 | 駿馬金龍](https://www.junmajinlong.com/linux/systemd/runlevel/) {% asset_link web2.png 備份圖 %}
[systemd timer：取代cron和at的定时任务 | 骏马金龙](https://www.junmajinlong.com/linux/systemd/systemd_timer/) {% asset_link web3.png 備份圖 %}
* [Linux Systemd 詳細介紹: Unit、Unit File、Systemctl、Target | 3C](https://www.3chy2.com.tw/3c%E8%B3%87%E8%A8%8A/linux-systemd-%E8%A9%B3%E7%B4%B0%E4%BB%8B%E7%B4%B9-unit%E3%80%81unit-file%E3%80%81systemctl%E3%80%81target/) {% asset_link web4.png 備份圖 %}
* [Linux系列文章 | 骏马金龙](https://www.junmajinlong.com/linux/index/#systemd)

### 與supervisor 差異

* [systemd vs supervisord_spacewander - SegmentFault 思否](https://segmentfault.com/a/1190000009338835)
* [Systemd Supervisord 肤浅对比](https://liqiang.io/post/systemd-vs-supervisord)
* [systemd 和 supervisor 相比各有哪些优缺点？以及各自的适用场景是什么？ - 知乎](https://www.zhihu.com/question/48833333)

之前分不清楚systemd 和 supervisor 
最後看教學，一個是系統層級的、一個是應用層級的
最後還是決定學好 systemd

不過後來發現 timer 相關設定 supervisor 沒有相關設定

### 跟 docker 差異

* [Systemd与Docker的爱恨情仇 - DockOne.io](http://dockone.io/article/1093)
* [使用 systemd 管理 Docker container](https://blog.davy.tw/posts/use-systemd-to-manage-docker-container/)

之前有改想過systemd 管理docker 排程，但好像不建議這樣做...


### Failed to connect to bus: No such file or directory"

我們公司環境沒版法使用 `systemctl --user`
不知道是不是沒有 D-Bus關係
以下文章都嘗試過，還是沒辦法使用

* [linux - systemctl --use Failed to connect to bus: No such file or directory Debian 9 - Super User](https://superuser.com/questions/1561076/systemctl-use-failed-to-connect-to-bus-no-such-file-or-directory-debian-9)
* [systemd user "Failed to connect to bus: No such file or directory"](https://gist.github.com/Cretezy/dc64ed4519c63c3eb636fba354f93a1f)

* [docker centos 使用 systemctl Failed to get D-Bus connection: Operation not permitted_彻底拆分，一切可控！-CSDN博客](https://blog.csdn.net/tengdazhang770960436/article/details/78873869)
* [docker centos 使用 systemctl Failed to get D-Bus connection: Operation not permitted_彻底拆分，一切可控！-CSDN博客](https://blog.csdn.net/tengdazhang770960436/article/details/78873869)


XDG_RUNTIME_DIR=/run/user/`id -u`
~~建立資料夾??~~沒有用
https://answers.launchpad.net/ubuntu/+source/systemd/+question/287454

XDG_RUNTIME_DIR=/run/user/$UID systemctl --user status
我電腦 ElementaryOS 新家的參數

### 其他參數

#### work dir
[Trying to change WorkingDirectory of Systemd Service Unit | DigitalOcean](https://www.digitalocean.com/community/questions/trying-to-change-workingdirectory-of-systemd-service-unit)
```
WorkingDirectory=/home/pi
ExecStart=/home/pi/launchscript.sh
```
ExecStart 必須要完整路徑

#### timeout

[centos - How to change systemd service timeout value? - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/227017/how-to-change-systemd-service-timeout-value)

>TimeoutStartSec=

>    設置該服務允許的最大啟動時長。 如果守護進程未能在限定的時長內發出"啟動完畢"的信號，那麼該服務將被視為啟動失敗，並會被關閉。 如果未指定時間單位，那麼將視為以秒為單位。 例如設為"20"等價於設為"20s"。 設為 "infinity" 則表示永不超時。 當 Type=oneshot 時， 默認值為 "infinity" (永不超時)， 否則默認值等於 DefaultTimeoutStartSec= 的值(參見 systemd-system.conf(5) 手冊)。

>    如果一個 Type=notify 服務發送了 "EXTEND_TIMEOUT_USEC=…" 信號， 那麼允許的啟動時長將會在 TimeoutStartSec= 基礎上繼續延長指定的時長。 注意，必須在 TimeoutStartSec= 用完之前發出第一個延時信號。當啟動時間超出 TimeoutStartSec= 之後，該服務可以在維持始終不超時的前提下，不斷重複發送 "EXTEND_TIMEOUT_USEC=…" 信號， 直到完成啟動(發送 "READY=1" 信號)。詳見 sd_notify(3) 手冊。 
>TimeoutStopSec=

>    此選項有兩個用途： (1)設置每個 ExecStop= 的超時時長。如果其中之一超時， 那麼所有後繼的 ExecStop= 都會被取消，並且該服務也會被 SIGTERM 信號強制關閉。 如果該服務沒有設置 ExecStop= ，那麼該服務將會立即被 SIGTERM 信號強制關閉。 (2)設置該服務自身停止的超時時長。如果超時，那麼該服務將會立即被 SIGTERM 信號強制關閉(參見 systemd.kill(5) 手冊中的 KillMode= 選項)。 如果未指定時間單位，那麼將視為以秒為單位。 例如設為"20"等價於設為"20s"。 設為 "infinity" 則表示永不超時。 默認值等於 DefaultTimeoutStopSec= 的值(參見 systemd-system.conf(5) 手冊)。

>    如果一個 Type=notify 服務發送了 "EXTEND_TIMEOUT_USEC=…" 信號， 那麼允許的停止時長將會在 TimeoutStopSec= 基礎上繼續延長指定的時長。 注意，必須在 TimeoutStopSec= 用完之前發出第一個延時信號。當停止時間超出 TimeoutStopSec= 之後，該服務可以在維持始終不超時的前提下，不斷重複發送 "EXTEND_TIMEOUT_USEC=…" 信號，直到完成停止。 詳見 sd_notify(3) 手冊。 

[systemd.service 中文手冊 [金步國]](http://www.jinbuguo.com/systemd/systemd.service.html)

## Supervisor

[Ubuntu下Supervisor安裝、配置和使用 | 程式前沿](https://codertw.com/%E4%BC%BA%E6%9C%8D%E5%99%A8/170578/)
日後看情況再研究
有看到 golang 版本
假如沒有權限可以考慮
但發現好像沒有 crontab 東西

不繼續研究了....

## 總結

不專業總結
網路上有很多都寫得很詳細
建議看網路上詳細
本篇重點在筆記


網路上沒什麼看到 systemd 搭配 docker 裡面跑
感覺 supervisor 真的比較多
但我覺得排程這個東西有必要做虛擬化跑?這個也是個問題

用 crond 和 systemd 這個問題也是一個問題
目前我待過三家公司都是用 crond 居多
但用 crond 必須控制分散程式執行時間
有了 systemd ，工程師不用做複雜設計就可以使用
前提要會操作，所以進入門檻稍微高一點

不知道這些 systemd 有沒有辦法紀錄到 elk 上面
但我覺得這之後有空再研究
畢竟我還不會用XD