---
title: lsyncd 做檔案同步實作紀錄
date: 2020-04-25 16:22:34
tags: [lsyncd]
categories: Linux
---


最近看到這幾篇使用 inotify 做文件同步感到有興趣
雖然現在工作上目前還沒用到
但這種情境感覺很常遇到
~~希望能用不用寫程式方法~~

* [Rsync + inotify 做文件实时同步 - 哈希](https://www.haxi.cc/archives/Rsync-inotify%E5%81%9A%E6%96%87%E4%BB%B6%E5%AE%9E%E6%97%B6%E5%90%8C%E6%AD%A5.html)
* [Rsync + sersync 实现文件实时同步 - 哈希](https://www.haxi.cc/archives/Rsync-sersync%E5%AE%9E%E7%8E%B0%E6%96%87%E4%BB%B6%E5%AE%9E%E6%97%B6%E5%90%8C%E6%AD%A5.html)
* [rsync + lsyncd 实现文件实时同步 - 哈希](https://www.haxi.cc/archives/rsync-lsyncd%E5%AE%9E%E7%8E%B0%E6%96%87%E4%BB%B6%E5%AE%9E%E6%97%B6%E5%90%8C%E6%AD%A5-1.html)

<!--more-->

## 相關工具

* inotify

[inotify-tools 監聽資料夾裡檔案異動工具 | 程式狂想筆記](https://malagege.github.io/blog/2020/01/12/inotify-tools-%E7%9B%A3%E8%81%BD%E8%B3%87%E6%96%99%E5%A4%BE%E8%A3%A1%E6%AA%94%E6%A1%88%E7%95%B0%E5%8B%95%E5%B7%A5%E5%85%B7/)
之前小記這篇，其實裡面沒什麼實作
需透過 bash 執行
但這樣當然不是很方便，所以有類似 sersync 和 lsyncd 工具解決這個方案

* sersync

這篇[lsyncd实时同步搭建指南——取代rsync+inotify-Linux运维日志](https://www.centos.bz/2017/08/lsyncd-rsync-inotify/)有講到很多優點缺點{% asset_link web1.png 備份圖 %}
為了不浪費時間
所以我就不實作了

## lsyncd

### 安裝

apt install rsyncd

或去 github 官網抓 [axkibe/lsyncd: Lsyncd (Live Syncing Daemon) synchronizes local directories with remote targets](https://github.com/axkibe/lsyncd)

#### lua 記得要升到 5.2

Lua >= 5.2

Lsyncd depends on Lua 5.2 or greater; that is 5.2 or 5.3. For most distributions you need to install the liblua??, the liblua??-dev and the lua?? package, with ?? being the respective Lua version.
cmake >= 2.8

To configure Lsyncd to your system, cmake >= 2.8 is required
rsync >= 3.1

During runtime Lsyncd needs rsync > 3.1 installed both on source and target systems.

[Lsyncd - Building](https://axkibe.github.io/lsyncd/manual/building/)


### 設定

一開始不會有`/etc/lsyncd.conf`
可以做 `service lsync status` 有沒有執行


~~vim /etc/lsyncd.conf~~ 網路上看到是這個路徑
但是我看`CONFIG=/etc/lsyncd/lsyncd.conf.lua`

``` conf
# 全域設定
settings {
    logfile = "/var/log/lsyncd/lsyncd.log",
    statusFile = "/var/log/lsyncd/lsyncd.status"
    inotifyMode = "CloseWrite",
    maxProcesses = 8,
}

## 以下設定參考 https://www.centos.bz/2017/08/lsyncd-rsync-inotify/
# 可設定多組
# I. 本地目錄同步，direct：cp/rm/mv。 適用：500+萬文件，變動不大
sync {
    default.direct,
    source    = "/tmp/src",
    target    = "/tmp/dest",
    delay = 1
    maxProcesses = 1
    }

# II. 本地目錄同步，rsync模式：rsync
sync {
    default.rsync,
    source    = "/tmp/src",
    target    = "/tmp/dest1",
    excludeFrom = "/etc/rsyncd.d/rsync_exclude.lst",
    rsync     = {
        binary = "/usr/bin/rsync",
        archive = true,
        compress = true,
        bwlimit   = 2000
        } 
    }

# III. 遠程目錄同步，rsync模式 + rsyncd daemon
sync {
    default.rsync,
    source    = "/tmp/src",
    target    = "syncuser@172.29.88.223::module1",
    delete="running",  # 可以改成false 這樣就不會刪掉
    exclude = { ".*", ".tmp" },
    delay = 30,
    init = false, # 默認init功能設置為false來跳過初始rsync過程
    rsync     = {
        binary = "/usr/bin/rsync",
        archive = true,
        compress = true,
        verbose   = true,
        password_file = "/etc/rsyncd.d/rsync.pwd",
        _extra    = {"--bwlimit=200"}
        }
    }

# IV. 遠程目錄同步，rsync模式 + ssh shell
sync {
    default.rsync,
    source    = "/tmp/src",
    target    = "172.29.88.223:/tmp/dest",
    # target    = "root@172.29.88.223:/remote/dest",
    # 上面target，注意如果是普通用戶，必須擁有寫權限
    maxDelays = 5,
    delay = 30,
    # init = true,
    rsync     = {
        binary = "/usr/bin/rsync",
        archive = true,
        compress = true,
        bwlimit   = 2000
        # rsh = "/usr/bin/ssh -p 22 -o StrictHostKeyChecking=no"
        # 如果要指定其它端口，請用上面的rsh
        }
    }

# V. 遠程目錄同步，rsync模式 + rsyncssh，效果與上面相同
sync {
    default.rsyncssh,
    source    = "/tmp/src2",
    host      = "172.29.88.223",
    targetdir = "/remote/dir",
    excludeFrom = "/etc/rsyncd.d/rsync_exclude.lst",
    # maxDelays = 5,
    delay = 0,
    # init = false,
    rsync    = {
        binary = "/usr/bin/rsync",
        archive = true,
        compress = true,
        verbose   = true,
        _extra = {"--bwlimit=2000"},
        },
    ssh      = {
        port  =  1234
        }
    }
```

**default.direct**

Default.direct可以用來保持兩個本地目錄同步，比使用default.rsync更好的性能。Default.direct在啟動時使用（就像default.rsync一樣）rsync來初始化目標目錄與源目錄的同步。但是，在正常操作期間，default.direct使用/ bin / cp，/ bin / rm和/ bin / mv來保持同步。所有參數就像default.rsync一樣。

delete 也適用這個屬性

**_extra  可放一些參數**

想說有沒有 size-only 設定
但發現都找不到
後來看到`_extra` 可以設定

### 簡單測試

預設是 10 秒檢查資料更新
可設定 statusInterval
[Lsyncd - The Configuration File](https://axkibe.github.io/lsyncd/manual/config/file/)

```lua
settings {
   logfile    = "/tmp/lsyncd.log",
   statusFile = "/tmp/lsyncd.status",
   nodaemon   = true,
}

sync {
   default.rsync,
   source = "/home/malagege/test_lsyncd/a/",
   target = "/home/malagege/test_lsyncd/b/",
```

default.rsync 預設是執行
/usr/bin/rsync -ltsd --delete --include-from=- --exclude=* SOURCE TARGET
所以會做刪除動作

## 同步完做動作

[Lsyncd - FAQ: How can I call a script before or after each rsync operation?](https://axkibe.github.io/lsyncd/faq/postscript/)

```sh
#!/bin/bash
/usr/bin/rsync "$@"
result=$?
(
  if [ $result -eq 0 ]; then
     echo "my commands";
  fi
) >/dev/null 2>/dev/null </dev/null

exit $result
```

這邊注意 rsync 並不是對單一檔案做搬移
而是對整個...
echo $@ 結果是
`-slt -r --delete --ignore-errors --force --from0 --include-from=- --exclude=* /home/malagege/test_lsyncd/a/ /home/malagege/test_lsyncd/a/ /home/malagege/test_lsyncd/b/`

跟我想要抓的東西不一樣

## 可能做 Layer 2 做自訂動作

感覺這邊自由度很高
因為我對 lua 不熟
所以就沒打算做這段

* [關於使用Lsyncd進行數據鏡像同步 | 退思園](https://gowa.club/Linux-Unix/%E5%85%B3%E4%BA%8E%E4%BD%BF%E7%94%A8Lsyncd%E8%BF%9B%E8%A1%8C%E6%95%B0%E6%8D%AE%E9%95%9C%E5%83%8F%E5%90%8C%E6%AD%A5.html) {% asset_link web2.png 備份圖 %}
裡面有官方中文說明


## 其他參考

* [Lsyncd - Examples](https://axkibe.github.io/lsyncd/manual/examples/)
* [Lsyncd搭建同步镜像-用Lsyncd实现本地和远程服务器之间实时同步 - 江召伟 - 博客园](https://www.cnblogs.com/jiangzhaowei/p/8298416.html)
* [Bash中的IO與重定向 | 退思園](https://gowa.club/Linux-Unix/Bash%E4%B8%AD%E7%9A%84IO%E4%B8%8E%E9%87%8D%E5%AE%9A%E5%90%91.html)
* [lsyncd+rsync 实时同步](http://news.migage.com/articles/lsyncdrsync+%E5%AE%9E%E6%97%B6%E5%90%8C%E6%AD%A5_3575751_csdn.html)
* [lsyncd实时同步搭建指南——取代rsync+inotify-Linux运维日志](https://www.centos.bz/2017/08/lsyncd-rsync-inotify/)
* [做网站迁移 大量的小文件怎么办？ - 知乎](https://www.zhihu.com/question/26463409)


### 同步腳本

以下都是參考:[真正的inotify+rsync实时同步 彻底告别同步慢 - 琴酒网络 - 博客园](https://www.cnblogs.com/ginvip/p/6430986.html)

```bash
#!/bin/bash
/usr/bin/inotifywait -mrq --format '%w%f'-e create,close_write,delete /backup |while read file
#把發生更改的文件列表都接收到file 然後循環，但有什麼鬼用呢？下面的命令都沒有引用這個$file 下面做的是全量rsync
do
    cd /backup && rsync -az --delete /backup/ rsync_backup@192.168.24.101::backup/--password-file=/etc/rsync.password
done
```

優化

```bash
#!/bin/bash
src=/data/                           # 需要同步的源路徑
des=data                             # 目標服務器上 rsync --daemon 發佈的名稱，rsync --daemon這裡就不做介紹了，網上搜一下，比較簡單。
rsync_passwd_file=/etc/rsyncd.passwd            # rsync驗證的密碼文件
ip1=192.168.0.18                 # 目標服務器1
ip2=192.168.0.19                 # 目標服務器2
user=root                            # rsync --daemon定義的驗證用戶名
cd ${src}                            
# 此方法中，由於rsync同步的特性，這裡必須要先cd到源目錄，inotify再監聽 ./ 才能rsync同步後目錄結構一致，有興趣的同學可以進行各種嘗試觀看其效果
/usr/local/bin/inotifywait -mrq --format  '%Xe %w%f' -e modify,create,delete,attrib,close_write,move ./ | while read file
# 把監控到有發生更改的"文件路徑列表"循環
do
        INO_EVENT=$(echo $file | awk '{print $1}')      # 把inotify輸出切割 把事件類型部分賦值給INO_EVENT
        INO_FILE=$(echo $file | awk '{print $2}')       # 把inotify輸出切割 把文件路徑部分賦值給INO_FILE
        echo "-------------------------------$(date)------------------------------------"
        echo $file
        #增加、修改、寫入完成、移動進事件
        #增、改放在同一個判斷，因為他們都肯定是針對文件的操作，即使是新建目錄，要同步的也只是一個空目錄，不會影響速度。
        if [[ $INO_EVENT =~ 'CREATE' ]] || [[ $INO_EVENT =~ 'MODIFY' ]] || [[ $INO_EVENT =~ 'CLOSE_WRITE' ]] || [[ $INO_EVENT =~ 'MOVED_TO' ]]         # 判斷事件類型
        then
                echo 'CREATE or MODIFY or CLOSE_WRITE or MOVED_TO'
                rsync -avzcR --password-file=${rsync_passwd_file} $(dirname ${INO_FILE}) ${user}@${ip1}::${des} &&
# INO_FILE變量代表路徑哦  -c校驗文件內容
                rsync -avzcR --password-file=${rsync_passwd_file} $(dirname ${INO_FILE}) ${user}@${ip2}::${des}
#仔細看 上面的rsync同步命令 源是用了$(dirname ${INO_FILE})變量 即每次只針對性的同步發生改變的文件的目錄(只同步目標文件的方法在生產環境的某些極端
#環境下會漏文件 現在可以在不漏文件下也有不錯的速度 做到平衡)
#然後用-R參數把源的目錄結構遞歸到目標後面 保證目錄結構一致性
        fi
        #刪除、移動出事件
        if [[ $INO_EVENT =~ 'DELETE' ]] || [[ $INO_EVENT =~ 'MOVED_FROM' ]]
        then
                echo 'DELETE or MOVED_FROM'
                rsync -avzR --delete --password-file=${rsync_passwd_file} $(dirname ${INO_FILE}) ${user}@${ip1}::${des} &&
                rsync -avzR --delete --password-file=${rsync_passwd_file} $(dirname ${INO_FILE}) ${user}@${ip2}::${des}
#看rsync命令 如果直接同步已刪除的路徑${INO_FILE}會報no such or directory錯誤 所以這裡同步的源是被刪文件或目錄的上一級路徑
#並加上--delete來刪除目標上有而源中沒有的文件，這裡不能做到指定文件刪除，如果刪除的路徑越靠近根，則同步的目錄月多，同步刪除的操作就越花時間。
#這裡有更好方法的同學，歡迎交流。
        fi
        #修改屬性事件 指 touch chgrp chmod chown等操作
        if [[ $INO_EVENT =~ 'ATTRIB' ]]
        then
                echo 'ATTRIB'
                if [ ! -d "$INO_FILE" ]
# 如果修改屬性的是目錄 則不同步，因為同步目錄會發生遞歸掃瞄，等此目錄下的文件發生同步時，rsync會順帶更新此目錄。
                then
                        rsync -avzcR --password-file=${rsync_passwd_file} $(dirname ${INO_FILE}) ${user}@${ip1}::${des} &&         
                        rsync -avzcR --password-file=${rsync_passwd_file} $(dirname ${INO_FILE}) ${user}@${ip2}::${des}
                fi
        fi
done
```

簡單來看就是對檔案做 dirname 備份，減少做多次遞迴
是不是能確認來源是資料夾或檔案，單純 scp 或 rsync 過去是不是比較快?

## 相關同步數量問題

以下參考:[等很久資訊室: Lsyncd - Live syncing Daemon 資料夾同步](https://www.iq180.com.tw/2018/08/lsyncd-live-syncing-daemon.html)

檢查可監測數量設定值：
lsyncd 是依靠 kernel 內建的檔案監測功能 inotify，來回報記錄檔案狀態，而 inotify 預設有設定了一個可監測資料夾數量的限定值。

cat /proc/sys/fs/inotify/max_user_watches

在 CentOS 6.8 內，它預設值是 8192，也就是它只能監測最多 8192 個資料夾。
而我們要同步的資料夾有多少，會不會超過這個數值呢？必竟，若是超過了，它就無法同步到了。

cat /var/run/lsyncd/lsyncd.status

在狀態檔中找到一行如下：
Inotify watching 1440 directories
那數字便是目前 lsyncd 所監測的資料夾數量。
如果您看到的數字很靠近 8192 或甚至超過，那就必須放大這個限定值。

echo '10000' > /proc/sys/fs/inotify/max_user_watches

這樣可以即時把限定值改成 10000，但重開機後它又會變成原本的 8192。
下面方法可以改變開機後的預設值：

把上述即時修改的指令放到 rc.local 之類的開機啟動檔內。
或是
在 /etc/sysctl.conf 內加上一行
fs.inotify.max_user_watches = 10000

這樣它重開機後，一樣會放大成我們設定的數值。
