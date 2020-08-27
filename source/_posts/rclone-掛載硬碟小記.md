---
title: rclone 掛載硬碟小記
date: 2019-10-14 20:58:51
tags: [rclone, linux, gdrive]
categories: Linux
---

最近公司有用 gcsfuse 做硬碟掛載
突然想到之前有看到使用 rclone 的文章
今天就小式了一下

<!--more-->

## 在 Window 掛載 rclone

參考[使用 rclone 在 Windows 下挂载 Google 个人 / 团队云盘 - R 酱小窝](https://blog.rhilip.info/archives/874/)實作

### 下載

rclone ： https://rclone.org/downloads/
winfsp ： http://www.secfs.net/winfsp/download/

**winfsp**一定要安裝，這個軟體跟掛載有關係

## 設定

`rclone config`

參考: [Linux下rclone简单教程(支持VPS数据同步,多种网盘,支持挂载) | 半醉浮生丶](https://ymgblog.com/2018/03/09/296/)
```

root@arm1:~# rclone config
2018/06/12 15:36:43 NOTICE: Config file “/root/.config/rclone/rclone.conf” not found – using defaults
No remotes found – make a new one
n) New remote
s) Set configuration password
q) Quit config
n/s/q> n       **********************选择 n，新建配置文件
name> test       **********************输入配置的名称
Type of storage to configure.
Choose a number from below, or type in your own value
1 / Alias for a existing remote
\ “alias”
2 / Amazon Drive
\ “amazon cloud drive”
3 / Amazon S3 Compliant Storage Providers (AWS, Ceph, Dreamhost, IBM COS, Minio)
\ “s3”
4 / Backblaze B2
\ “b2”
5 / Box
\ “box”
6 / Cache a remote
\ “cache”
7 / Dropbox
\ “dropbox”
8 / Encrypt/Decrypt a remote
\ “crypt”
9 / FTP Connection
\ “ftp”
10 / Google Cloud Storage (this is not Google Drive)
\ “google cloud storage”
11 / Google Drive
\ “drive”
12 / Hubic
\ “hubic”
13 / Local Disk
\ “local”
14 / Mega
\ “mega”
15 / Microsoft Azure Blob Storage
\ “azureblob”
16 / Microsoft OneDrive
\ “onedrive”
17 / Openstack Swift (Rackspace Cloud Files, Memset Memstore, OVH)
\ “swift”
18 / Pcloud
\ “pcloud”
19 / QingCloud Object Storage
\ “qingstor”
20 / SSH/SFTP Connection
\ “sftp”
21 / Webdav
\ “webdav”
22 / Yandex Disk
\ “yandex”
23 / http Connection
\ “http”
Storage> 11       **********************这里是添加 googledrive 云盘，选择 11，当然你也可以添加其它云盘
Google Application Client Id – leave blank normally.
client_id>       **********************回车留空即可
Google Application Client Secret – leave blank normally.
client_secret>       **********************回车留空即可
Scope that rclone should use when requesting access from drive.
Choose a number from below, or type in your own value
1 / Full access all files, excluding Application Data Folder.
\ “drive”
2 / Read-only access to file metadata and file contents.
\ “drive.readonly”
/ Access to files created by rclone only.
3 | These are visible in the drive website.
| File authorization is revoked when the user deauthorizes the app.
\ “drive.file”
/ Allows read and write access to the Application Data folder.
4 | This is not visible in the drive website.
\ “drive.appfolder”
/ Allows read-only access to file metadata but
5 | does not allow any access to read or download file content.
\ “drive.metadata.readonly”
scope>       **********************回车留空即可，这里是云盘 API 权限的设置
ID of the root folder – leave blank normally. Fill in to access “Computers” folders. (see docs).
root_folder_id>       **********************回车留空即可
Service Account Credentials JSON file path – leave blank normally.
Needed only if you want use SA instead of interactive login.
service_account_file>
Remote config
Use auto config?
* Say Y if not sure
* Say N if you are working on a remote or headless machine or Y didn’t work
y) Yes
n) No
y/n> n       **********************输入 n，手动配置
If your browser doesn’t open automatically go to the following link: https://accounts.google.com/o/oauth2/auth?access_type=offline&client_id=20111111644.apps.googleusercontent.com&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob&response_type=code&scope=https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fdrive&state=1111111111111111111111       **********************在本地打开这个链接，添加 google 云盘账号授予 rclone 权限
Log in and authorize rclone for access
Enter verification code> 4/AAC8CPF21dYWqRnNSw479Fa8dUb11111111111111111111111111E       **********************输入在浏览器获取的认证 code
Configure this as a team drive?
y) Yes
n) No
y/n> y       **********************是否团队云盘，我这里是，所以选择 Y
Fetching team drive list…
Choose a number from below, or type in your own value
1 / back
\ “0AAbWF01111111111”
2 / back2
\ “0ABf3gOL111111111”
Enter a Team Drive ID> 1       **********************选择一个你要添加的团队云盘
——————–
[test]
type = drive
client_id =
client_secret =
scope =
root_folder_id =
service_account_file =
token = {“access_token”:”ya29.GlvY1111qGBysX4tO_V5ZSO1PZ_Kq1111111111111111fksugvyOFC-MzvAPnl1rsi3OOwMFTdkk11111111H_u5zzPRp0eB_iLzKsZOVzP3R2ee1111AVmO”,”token_type”:”Bearer”,”refresh_token”:”1/r5Q_pH11111111CV-xiMne9_w62y_ag11bT_H9zz7qc”,”expiry”:”2018-06-12T16:37:16.714510063+08:00″}
team_drive = 0AA11111111111111
——————–
y) Yes this is OK
e) Edit this remote
d) Delete this remote
y/e/d> y       **********************输入 y，配置到这里就完成了
Current remotes:

Name Type
==== ====
test drive

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> q       **********************输入 q 退出即可！
```

### 掛載

```
rclone mount gdrive:/ x: --cache-dir D:\tmp --vfs-cache-mode writes
# git bash
rclone mount gdrive:/ x: --cache-dir /d/tmp --vfs-cache-mode writes
```

不知道為什麼我的 powershell 執行失敗...
但用 Git bash 就可以順力執行

### GUI

另外，GUI 介面:[Releases · kapitainsky/RcloneBrowser](https://github.com/kapitainsky/RcloneBrowser/releases)
這個我就沒有試了

## 在 Raspberry Pi 掛載

### 安裝

```bash
# raspberry pi 
curl -L https://raw.github.com/pageauc/rclone4pi/master/rclone-install.sh | bash
# normal(not raspberry pi)
curl https://rclone.org/install.sh | sudo bash
```

有看到`sudo apt install rclone`也可以使用，我沒有測試

rclone config 就不再寫了，請看上面

### 掛載

**這邊需要注意權限問題**

假如要掛載 `/mnt/xxx` 可能未因為 `user` 權限無法掛載
可以使用家目錄 或者吧 `/mnt/xxx` 改成 777
或者切換 `root` 做 mount

```
rclone mount --vfs-cache-mode writes --fast-list --vfs-cache-max-size 100M gdrive: /mnt/gdrive
```
有執行發現這一行不是背景執行...
除了用`&`
還發現有人用 tmux

### Tmux

```
tmux # create tmux 
tmux ls # tmux ls
tmux a -t 0# tmux session
Ctrl+b & d (關掉背景執行)
```

簡單說一下，`tmux`建立一個 session，做 mount 動做
這時候會發現不能做任何動作，`Ctrl+b & d`會回到原始的 bash
再輸入`tmux`，可以做`cp 動作到gdrive` 再`Ctrl+b & d`
關掉 command line 連線都還會在
`tmux ls`可以看到還有哪些 session 再跑

因為指令不熟又不常用，之前用過的都忘了差不多
有空再補...



### 自動掛載

假如用網路上範例
路徑存放在`/etc/systemd/system/rclone.service`
使用前建議用root掛載

但聽說一般 user 也能設定
我沒有仔細研究
`~/.config/systemd/user/rclone@.service`

改完記得要**systemctl daemon-reload**

(還沒測試)
```
[Unit]
Description=rclone: Remote FUSE filesystem for cloud storage config %i
Documentation=man:rclone(1)[Service]
Type=notify
ExecStartPre=/bin/mkdir -p %h/mnt/%i
ExecStart= \
  /usr/bin/rclone mount \
    --fast-list \
    --vfs-cache-mode writes \
    --vfs-cache-max-size 100M \
    %i: %h/mnt/%i[Install]
WantedBy=default.target
```

剛剛發現`--daemon`有這個，~~尚未測試~~測是這個可運作!!

```
[Unit]
Description=RClone Service
After=network-online.target
Wants=network-online.target

[Service]
Type=notify
ExecStart=/usr/sbin/rclone mount --allow-other --dir-cache-time 48h --vfs-read-chunk-size 16M --buffer-size 2G Googlecrypt: /home/plex
ExecStop=/bin/fusermount -uz /home/plex
Restart=on-abort
```


最終我電腦設定
```
[Unit]
Description=RClone Service
After=network-online.target
Wants=network-online.target

[Service]
User=pi
Type=notify
#ExecStart=/usr/bin/rclone mount --allow-root --vfs-cache-mode writes --vfs-cache-max-size 100M gdrive: /mnt/gdrive
ExecStart=/usr/bin/rclone mount --allow-root --vfs-cache-mode writes --vfs-cache-max-size 100M gdrive: /mnt/gdrive
ExecStop=/bin/fusermount -uz /mnt/gdrive
RestartSec=30

[Install]
WantedBy=multi-user.target
```

allow-root 不能用...，好像是 bug


- [Rclone auto mount - rclone - rclone forum](https://forum.rclone.org/t/rclone-auto-mount/6353)
- [rclone Systemd startup mount script – JamesCoyle.net Limited](https://www.jamescoyle.net/how-to/3116-rclone-systemd-startup-mount-script)
- [rclone fstab mount helper script · rclone/rclone Wiki](https://github.com/rclone/rclone/wiki/rclone-fstab-mount-helper-script)
- Restart 可參考[Systemd 入门教程：实战篇 - 阮一峰的网络日志](https://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-part-two.html)
- [systemd (正體中文) - ArchWiki](https://wiki.archlinux.org/index.php/Systemd_(%E6%AD%A3%E9%AB%94%E4%B8%AD%E6%96%87))
### 移除 rclone

[Home · pageauc/rclone4pi Wiki](https://github.com/pageauc/rclone4pi/wiki#how-to-remove-rclone)

### 使用 Google Driver 注意事項

- 1 天上傳最多 750 GB
- 1 個檔案不能超過 100 GB
- 不能太平凡掛載

超過小心帳號可能會被 Ban!!

### rsync 複製檔案顯示進度

```
rsync -rvh --progress /mnt/ddd /mnt/gdrive
```
-r 複製資料夾記得要加上去

影片不建議使用 rsync ，不知道為什麼丟到上面去，影片不會轉成線上看
下載檔案後面會加奇怪副檔名，所以用 `cp` 比較不會有問題

後來發現`rclone sync xxx gd:xxx -P` 也能看狀態= =

### Raspberry PI 掛載 USB

之前有在 [葉難: Raspberry Pi：自動掛載USB隨身碟](http://yehnan.blogspot.com/2016/05/raspberry-piusb.html) 看到怎麼使用
```
UUID="F840-AAE1"  /media/usbstick vfat   rw,defaults 0 0
```

後面兩個 0 代表
```
    第五欄：能否被 dump 備份指令作用：

dump 是一個用來做為備份的指令，不過現在有太多的備份方案了，所以這個項目可以不要理會啦！直接輸入 0 就好了！

    第六欄：是否以 fsck 檢驗磁區：

早期開機的流程中，會有一段時間去檢驗本機的檔案系統，看看檔案系統是否完整 (clean)。 不過這個方式使用的主要是透過 fsck 去做的，我們現在用的 xfs 檔案系統就沒有辦法適用，因為 xfs 會自己進行檢驗，不需要額外進行這個動作！所以直接填 0 就好了。
```
參考:[鳥哥的 Linux 私房菜 -- 第七章、Linux 磁碟與檔案系統管理](http://linux.vbird.org/linux_basic/0230filesystem.php#fstab)


1. sudo fdisk -l  (查看裝置有沒有掛載)
2. $ sudo mkdir /mnt/extHDD (建置掛載用的資料夾和權限)
$ sudo chown pi:pi /mnt/extHDD
3. sudo mount -t vfat  /dev/sda1 /mnt/extHDD # (test mount) ext4的話，把 vfat 改成 ext4
`-o uid=pi,gid=pi`我的樹梅派家這段會失敗
4. sudo umount /dev/sda1 # 解除掛載
5. sudo blkid # 查看掛載的 UUID
6. sudo vim /etc/fstab  
7. 加入 UUID="F840-AAE1"  /mnt/extHDD vfat   rw,defaults 0 0 #(-a, --all 掛載 fstab 中的所有文件系統)



[Mount的用法詳細解析 - IT閱讀](https://www.itread01.com/content/1553540779.html)

最近看到[使 Amazon EBS 磁碟區可供在 Linux 上使用 - Amazon Elastic Compute Cloud](https://docs.aws.amazon.com/zh_tw/AWSEC2/latest/UserGuide/ebs-using-volumes.html)這篇
UUID=aebf131c-6957-451e-8d34-ec978d9581ae  /data  xfs  defaults,**nofail**  0  2
> 如果曾在不掛載此磁碟區的狀態下開機執行個體 (例如，在將磁碟區移至其他執行個體後)，nofail 掛載選項可讓執行個體繼續開機，即使在掛載磁碟區的作業出現錯誤。包括 16.04 前之 Ubuntu 版本在內的 Debian 衍生產品，也必須新增 nobootwait 掛載選項。 

/usr/bin/rclone mount --allow-root --vfs-cache-mode writes --vfs-cache-max-size 100M gdrive: /mnt/gdrive
2019/10/16 16:15:54 mount helper error: fusermount: option allow_root only allowed if 'user_allow_other' is set in /etc/fuse.conf

### systemctl 和 service 掛載的差異

在這次藉著學習到簡單使用 systemctl 設定服務
但很好奇這兩個差異

> systemd是Linux系統最新的初始化系統(init),作用是提高系統的啟動速度，儘可能啟動較少的進程，儘可能更多進程並發啟動。
> systemd對應的進程管理命令是systemctl

參考:[Linux 服務管理兩種方式service和systemctl - 迪米特 - 博客園](https://www.cnblogs.com/shijingjing07/p/9301590.html)
裡面還有講:`主要有四種類型文件.mount,.service,.target,.wants`

有找到比較兩者指令差異[centos7 systemctl取代service和chkconfig来实现系统管理 - cape的博客 - CSDN博客](https://blog.csdn.net/capecape/article/details/78505511) {% asset_link web1.png 備份圖 %}

發現選擇 systemctl 還是能繼續用 service 指令
習慣改不了用 service XDD

但設定上 service 設定服務需要用 chkconfig
用systemctl 直接 enable/disable 就能解決

[systemd (简体中文) - ArchWiki](https://wiki.archlinux.org/index.php/Systemd_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
[systemd/User (简体中文) - ArchWiki](https://wiki.archlinux.org/index.php/Systemd/User_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

`journalctl` 查看 systemctl log
```
#查看所有日誌（默認情況下 ，只保存本次啟動的日誌） $ sudo journalctl #查看內核日誌（不顯示應用日誌） 
$ sudo journalctl -k #查看系統本次啟動的日誌 
$ sudo journalctl -b 
$ sudo journalctl -b -0 #查看上一次啟動的日誌（需更改設置） 
$ sudo journalctl -b -1 #查看指定時間的日誌 
$ sudo journalctl --since="2017-09-26 22:17:16" 
$ sudo journalctl --since "30 min ago" 
$ sudo journalctl --since yesterday 
$ sudo journalctl --since "2015-01-10" --until "2017-01-11 03:00" 
$ sudo journalctl --since 09:00 --until "1 hour ago" #顯示尾部的最新10行日誌 
$ sudo journalctl -n #顯示尾部指定行數的日誌 
$ sudo journalctl -n 50 #實時滾動顯示最新日誌 
$ sudo journalctl -f #查看指定服務的日誌 
$ sudo journalctl /usr/lib/systemd/systemd #查看指定進程的日誌 
$ sudo journalctl _PID=1 #查看某個路徑的腳本的日誌 
$ sudo journalctl /usr/bin/bash #查看指定用戶的日誌 
$ sudo journalctl _UID=33 --since today #查看某個 Unit 的日誌 
$ sudo journalctl -u nginx.service 
$ sudo journalctl -u nginx.service --since today #實時滾動顯示某個 Unit 的最新日誌 
$ sudo journalctl -u nginx.service -f #合併顯示多個 Unit 的日誌 
$ journalctl -u nginx.service -u php-fpm.service --since today #查看指定優先級（及其以上級別）的日誌，共有8級
 # 0: emerg
 # 1: alert
 # 2: crit
 # 3: err
 # 4: warning
 # 5: notice
 # 6: info
 # 7: debug 
$ sudo journalctl -p err -b #日誌默認分頁輸出，--no-pager 改為正常的標準輸出 
$ sudo journalctl --no-pager #以 JSON 格式（單行）輸出 
$ sudo journalctl -b -u nginx.service -o json #以 JSON 格式（多行）輸出，可讀性更好 
$ sudo journalctl -b -u nginx.serviceqq -o json-pretty #顯示日誌占據的硬碟空間 
$ sudo journalctl --disk-usage #指定日誌文件占據的最大空間 
$ sudo journalctl --vacuum-size=1G #指定日誌文件保存多久 
$ sudo journalctl --vacuum-time=1years

原文網址：https://kknews.cc/code/pqyayy2.html
```

相關網頁
- [systemd一種更好的啟動方式 - 每日頭條](https://kknews.cc/zh-tw/code/pqyayy2.html)
- [鳥哥的 Linux 私房菜 -- 第十七章、認識系統服務 (daemons)](http://linux.vbird.org/linux_basic/0560daemons.php#systemd_cfg_vsftpd)
- [玩转 systemd 之用户级服务管理 - 依云's Blog](https://blog.lilydjwg.me/2014/2/2/systemd-user-daemons.42631.html)

聽說一般 user 家目錄也可以做服務設定
這一快我就先不研究
改天用到再說

**2020-04-26**

這些查詢滿常用的

```bash
# 檢查 nginx 服務是否正在運行
systemctl is-active nginx.service

# 檢查 nginx 服務是否有設定開機自動啟動
systemctl is-enabled nginx.service

# 檢查 nginx 服務是否啟動失敗
systemctl is-failed nginx.service
```

### tmux 掛載方式(不建議用)

在沒知道用 systemctl 原本想使用 tmux
不過看起來好像能跑，但我覺得 systemctl 比較完美

[tmux script example](https://gist.github.com/B-Galati/c7890f86fc2bfce0b98c5410d964727a)

```bash
#!/bin/bash
tmux new-window -d -n 'test' 'ping 127.0.0.1'
```

## tranmission 

```
"script-torrent-done-enabled": true,
"script-torrent-done-filename": "/home/user/script.sh",
```
記得 chmod a+x /home/user/script.sh
裡面對印的
conf 也要加 777 (tranmission不同權限，或加群組權限)
```
# 注意需要看.config/rclone 資料夾和檔案權限，需要到rx
RCLONE_CONFIG_PATH="--config=/home/pi/.config/rclone/rclone.conf"
```
[Transmission download complete shell script for moving complete files and removing its torrent entry](https://gist.github.com/awesometic/253b740d45f8e5f95b56ec24f33a9444)
[Script to clear finished torrents from transmission-daemon](https://gist.github.com/pawelszydlo/e2e1fc424f2c9d306f3a)
[Guide : Auto removal of downloads from transmission 2.82 - My Cloud - Personal Cloud Storage / My Cloud - WD Community](https://community.wd.com/t/guide-auto-removal-of-downloads-from-transmission-2-82/93156)
[Tmux 開啟後 自動 SSH 連結多台指定機器 | Tsung's Blog](https://blog.longwin.com.tw/2013/05/tmux-auto-ssh-remote-2013/)
[Transmission - Script to move completed downloads to directories, or how to create script? - Plugins - openmediavault](https://forum.openmediavault.org/index.php/Thread/13290-Transmission-Script-to-move-completed-downloads-to-directories-or-how-to-create/)
[ubuntu - Copy completed files larger than 1024M to another directory - Super User](https://superuser.com/questions/735691/copy-completed-files-larger-than-1024m-to-another-directory)
[ubuntu - Copy completed files larger than 1024M to another directory - Super User](https://superuser.com/questions/735691/copy-completed-files-larger-than-1024m-to-another-directory/735757#735757)

密碼不要取驚嘆號
[技术|在Linux命令行下令人惊叹的惊叹号（!）](https://linux.cn/article-5608-1.html)

```
Oct 18 23:35:12 raspberrypi transmission-daemon[13077]: 2019/10/18 23:35:12 Failed to save config after 10 tries: Failed to create temp file for new config: open /home/pi/.config/rclone/rclone.conf295018896: permission denied
Oct 18 23:35:17 raspberrypi transmission-daemon[13077]: 2019/10/18 23:35:17 Failed to save config after 10 tries: Failed to create temp file for new config: open /home/pi/.config/rclone/rclone.conf984318943: permission denied
```

我改寫的 Script
有通知 discord 功能
```bash
#!/bin/bash

DSCORD_WEBHOOK="https://discordapp.com/api/webhooks/*"
# 需要做同步的路徑，結尾資料夾不用/
LOCAL_SYNCDIR="/mnt/extHDD/Download"
# RCLONE 結尾不能有 /
RCLONE_SYNCDIR="gdrive:/test"
# 注意需要看.config/rclone 資料夾和檔案權限，需要到rx
RCLONE_CONFIG_PATH="--config=/home/pi/.config/rclone/rclone.conf"
AUTH="--auth admin:admin"
#### debug end
df -H | grep -vE '^Filesystem|tmpfs|cdrom|udev|已用' | awk '{ print $5 " " $1 }' | while read output;
do
  usep=$(echo $output | awk '{ print $1}' | cut -d'%' -f1  )
  partition=$(echo $output | awk '{ print $2 }' )
  if [ $usep -ge 80 ]; then
    msg="⚠️注意快超出硬碟空間\\\"$partition\\\" ($usep%) on $(hostname) as on $(date)"
    url=$DSCORD_WEBHOOK
    curl -H "Content-Type: application/json" \
    -X POST \
    -d "{\"username\": \"system\", \"content\": \"${msg}\"}" $url
  fi
done





if [[ "$TR_TORRENT_DIR" =~ ^"$LOCAL_SYNCDIR" ]]; then
    echo "pass"
    if [ -f "${TR_TORRENT_DIR}/${TR_TORRENT_NAME}" ]; then
        flock -x  /tmp/rclone.lock rclone copy ${RCLONE_CONFIG_PATH} "${TR_TORRENT_DIR}/${TR_TORRENT_NAME}" "$RCLONE_SYNCDIR"
        rclone check ${RCLONE_CONFIG_PATH} "${TR_TORRENT_DIR}/${TR_TORRENT_NAME}" "$RCLONE_SYNCDIR"
    else 
        flock -x  /tmp/rclone.lock rclone copy ${RCLONE_CONFIG_PATH} "${TR_TORRENT_DIR}/${TR_TORRENT_NAME}" "$RCLONE_SYNCDIR/${TR_TORRENT_NAME}"
        rclone check ${RCLONE_CONFIG_PATH} "${TR_TORRENT_DIR}/${TR_TORRENT_NAME}" "$RCLONE_SYNCDIR/${TR_TORRENT_NAME}" --size-only
    fi
    
    if [ $? -eq 0 ]; then
        msg="✅ $TR_TORRENT_NAME 完成下載，並完成同步"
        echo $msg
        url=$DSCORD_WEBHOOK
        curl -H "Content-Type: application/json" \
        -X POST \
        -d "{\"username\": \"system\", \"content\": \"${msg}\"}" $url
        transmission-remote $AUTH -t $TR_TORRENT_ID --remove-and-delete
    else
        echo "FAIL"
        msg="⚠️ $TR_TORRENT_NAME 完成下載，但同步失敗"
        echo $msg
        url=$DSCORD_WEBHOOK
        curl -H "Content-Type: application/json" \
        -X POST \
        -d "{\"username\": \"system\", \"content\": \"${msg}\"}" $url
    fi
    
else
    echo "✅$TR_TORRENT_NAME 完成下載，不需要做同步"
    msg="✅$TR_TORRENT_NAME 完成下載，不需要做同步"
    echo $msg
    url=$DSCORD_WEBHOOK
    curl -H "Content-Type: application/json" \
    -X POST \
    -d "{\"username\": \"system\", \"content\": \"${msg}\"}" $url
fi

```

有時候會失敗，補跑方式也很簡單
```bash
export TR_TORRENT_DIR="/mnt/extHDD/Download"
ls $TR_TORRENT_DIR

export TR_TORRENT_NAME="xxx"

#測試
#echo ${TR_TORRENT_DIR}/${TR_TORRENT_NAME}
ls "${TR_TORRENT_DIR}/${TR_TORRENT_NAME}"

/etc/transmission-daemon/syncGdrive.sh
```

最近這個腳本還算完美的
不過處理大檔案`rclone check `太久了

> → thousandday: rclone sync比對修改時間和size(很快,省流量) 05/29 18:17
> → thousandday: rclone check --size-only比對size(很快,省流量) 05/29 18:18
> → thousandday: rclone sync -c完整下載後Checksum(慢,費流量) 05/29 18:18
> → thousandday: rclone check下載hash值後Checksum(慢,省流量) 05/29 18:18
> → thousandday: Google Drive有支援hash可以直接撈到MD5 05/29 18:19
> → thousandday: 沒支援hash的雲端空間要Checksum只能完整下載 05/29 18:19
> → thousandday: 用rclone sync -c或rclone check --download 05/29 18:19
> → thousandday: 個人認為rclone sync夠用了 Checksum很花時間和效能 05/29 18:20
> → thousandday: 檔案如果多又大 好幾TB會跑到天荒地老
[[工具] 自製本機與google drive檔案md5比對工具 - 看板 Free_box - 批踢踢實業坊](https://www.ptt.cc/bbs/Free_box/M.1527420602.A.819.html)

我後來加`--size-only`
覺得應該不會這麼衰...
不然好幾GB都卡很久

### aria2(不建議使用)

除非你想用...

安裝方法
```
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubiBackup/doubi/master/aria2.sh && chmod +x aria2.sh && bash aria2.sh
```
[『原创』BT/种子/磁力链接下载工具 —— Aria2 一键安装管理脚本 | 逗比根据地](https://doubibackup.com/zuigoj__.html?fbclid=IwAR22FvWyZjXt8qwW2IQfYGOPbmewh6kyRpqvxSX3Zd0aWKJQAx1p777hOCo)
但說真的這個腳本整合超好的

幫我同學用的
結果用別人 Script 搬到 rclone 不能正常
所以自己改寫，可能有 bug
請自行修

有看到 *.aria2 檔案有刪除
重開機有時候回重新載(然後aira2檔案又會出現...)
不知道是不是用 aria2 會有這樣問題?

$3 不是傳資料夾位址
所以在查哪個資料夾會不太方便
所以檔案一定要下載在 downloadpath 才會做上傳動作

Script 是參考這個網站
但好像連不到了(在我寫這篇前幾天掛掉...)
[使用 Aria2 + rclone 遇到的一些问题 | 代码真香](https://webcache.googleusercontent.com/search?q=cache:f9-JvXWtHC0J:https://blog.codesofun.com/aria2-rclone-faq.html+&cd=7&hl=zh-TW&ct=clnk&gl=tw&client=firefox-b-d)
備份到 [Plunker - demo script](https://next.plnkr.co/edit/DOfCgSvBCebpklXE?preview)
Youtube 還活著 [代码真香 - YouTube](https://www.youtube.com/channel/UCmlhPmTdqYhRWwWZWSIBwGw?feature=embeds_subscribe_title)

目前 $3 是傳第一個檔案內容
這邊就跟 tranmission 不太一樣，tranmission 給的參數比較完整
發現原 Script 會複製全部目錄到 rclone 去
這樣多線程下載的檔案會有問題
所以依照來看，舊的 Script 應該是 **1線程** 下載 (它裡面網頁沒寫...，不過看他網頁寫 VPS 10GB 看來是有可能的....)

因為我不熟 shell ，以下用的出事不要找我XD

```bash
#!/bin/bash

filepath=$3	 #取文件原始路径，如果是单文件则为/Download/a.mp4，如果是文件夹则该值为文件夹内第一个文件比如/Download/a/1.mp4
path=${3%/*}	 #取文件根路径，如把/Download/a/1.mp4变成/Download/a
downloadpath='/media/download'	#Aria2下载目录
name='gdrive' #配置Rclone时的name
folder='/test2'	 #网盘里的文件夹，如果是根目录直接留空
MinSize='10k'	 #限制最低上传大小，默认10k，BT下载时可防止上传其他无用文件。会删除文件，谨慎设置。
MaxSize='15G'	 #限制最高文件大小，默认15G，OneDrive上传限制。


if [ $2 -eq 0 ]; then exit 0; fi

if [ `dirname "$filepath"` =  "$downloadpath" ] && [ $2 -eq 1 ]	#如果下载的是单个文件
    then
    echo "enter 1" 
    rclone move -v "$filepath" ${name}:${folder} --min-size $MinSize --max-size $MaxSize
    rm -vf "$filepath".aria2	#删除残留的.aria.2文件
    echo result: $?
    exit 0
elif [[ "$path" =~ ^"$downloadpath" ]]	#如果下载的是文件夹
    then
    echo "enter 2"
    echo path_length ${#path}

    a=$downloadpath
    b="$filepath"
    c=${b#$a/}
    d=${c%%/*}
    echo d = $d
    echo $path/$d/

    rclone move -v "$downloadpath/$d/" ${name}:/${folder}/"${d}" --min-size $MinSize --max-size $MaxSize --delete-empty-src-dirs
    #rclone delete -v "$downloadpath/$d/" --max-size $MinSize	#删除多余的文件
    #rclone rmdirs -v "$downloadpath/$d/" --leave-root	#删除空目录，--delete-empty-src-dirs 参数已实现，加上无所谓。
    rm -vf "$downloadpath/$d".aria2	#删除残留的.aria2文件
    rmdir "$downloadpath/$d"
    echo result: $?
    exit 0
fi
```

a="123"
b="123/4/125/1236"
c=${b#$a/}
d=${c%%/*}

[shell中#*,##*,#*,##*,% *,%% *的含义及用法 - jiezi2016的博客 - CSDN博客](https://blog.csdn.net/jiezi2016/article/details/79649382)

## 參考來源

* [Linux下rclone简单教程(支持VPS数据同步,多种网盘,支持挂载) | 半醉浮生丶](https://ymgblog.com/2018/03/09/296/)
* [Raspberry Pi microSD card performance comparison - 2019 | Jeff Geerling](https://www.jeffgeerling.com/blog/2019/raspberry-pi-microsd-card-performance-comparison-2019)
* [树莓派raspberrypi3打造homeassistant+homebridge智能家居中心（homekit） - 知乎](https://zhuanlan.zhihu.com/p/31004760)
* [raspbian - How to set VNC security settings using SSH? `No security types supported. Server sent security types, but we do not support any of them` - Raspberry Pi Stack Exchange](https://raspberrypi.stackexchange.com/questions/63586/how-to-set-vnc-security-settings-using-ssh-no-security-types-supported-server)
* [解决Rclone挂载Google Drive时上传失败和内存占用高等问题 - Rat's Blog](https://www.moerats.com/archives/877/)
* [利用Aria2+Nginx+rclone完成无限电影硬盘 | Sandy'Blog](https://blog.e9china.net/lesson/liyongaria2nginxrclonewanchengwuxiandianyingyingpan.html)
* [Plex + Rclone 掛載 Google drive 搭建多媒體伺服器 - WIJ Blog](https://wijtb.nctu.me/archives/153/)
* [[筆記]將Google Drive掛載到NAS上(用rclone) @ 老電影下載 [ Old Movies ] :: 痞客邦 ::](https://ak77now.pixnet.net/blog/post/45722625-mount-google-drive-on-nas-with-rclone)
* [使用 rclone 在 Windows 下挂载 Google 个人 / 团队云盘 - R 酱小窝](https://blog.rhilip.info/archives/874/)
* [【转载】Rclone: 超好用的网盘 / VPS 数据同步、备份工具，支持 Google Drive | 初行博客](https://www.zrj96.com/post-520.html)
* [Rclone 进阶使用教程：常用命令参数详解 - P3TERX ZONE - Zhuangbility & Technology](https://p3terx.com/archives/rclone-advanced-user-manual-common-command-parameters.html)
* [[心得] Rclone Browser教學 - 看板 Free_box - 批踢踢實業坊](https://www.ptt.cc/bbs/Free_box/M.1529958022.A.295.html)
* [Screenly - How to automatically turn off and on your monitor from your Raspberry Pi](https://www.screenly.io/blog/2017/07/02/how-to-automatically-turn-off-and-on-your-monitor-from-your-raspberry-pi/)
* [Raspberry Pi - 超赞合集 awesome list chinese](https://asmcn.icopy.site/awesome/awesome-raspberry-pi/)
* [About script torrent done filename · ronggang/transmission-web-control Wiki](https://github.com/ronggang/transmission-web-control/wiki/About-script-torrent-done-filename)
* [Scripts · transmission/transmission Wiki](https://github.com/transmission/transmission/wiki/Scripts)
* [LearnHouse » Blog Archive » [Raspberry Pi 3]開機自動掛載 USB 隨身碟或外接硬碟](https://learn-house.idv.tw/?p=2340)
* [伺服器架設/防火牆建置 - HackMD](https://hackmd.io/@Pingu/SyZ1Pq7mQ/https%3A%2F%2Fhackmd.io%2Fs%2FSyP9NaGpm%23?type=book)
* [CodeIgniter用ChromePHP來debug[PHP] | 敲敲打打的生活](http://blog.ipushs.com/codeigniter%e7%94%a8chromephp%e4%be%86debugphp/)
* [bash wait exit on error code - Stack Overflow](https://stackoverflow.com/questions/49513335/bash-wait-exit-on-error-code)
* [Mounting Google Drive on Raspberry Pi - Artur Klauser - Medium](https://medium.com/@artur.klauser/mounting-google-drive-on-raspberry-pi-f5002c7095c2)
* [Google Drive 相互複製 對拷 – BunnyBit 比特小兔兔](https://0xhack.wordpress.com/2019/02/27/google-drive-%E7%9B%B8%E4%BA%92%E8%A4%87%E8%A3%BD-%E5%B0%8D%E6%8B%B7/)
* [Documentation](https://rclone.org/docs/#bwlimit-bandwidth-spec) 限速??待確認


**2020-07-23**

設定時間排程 at
Raspberry Pi 預設沒有安裝
```bash
sudo apt install at
at :time 
# at -l = atq
atq
# atrm jobid
at now + 5 minutes 


```
中斷結束 Ctrl + D


[transmission-remote(1) - Linux man page](https://linux.die.net/man/1/transmission-remote)
下載
transmission-remote --auth=admin:admin   -tall --start
上傳
transmission-remote --auth=admin:admin   -tall --stop



**20200808**
更新放置路徑可用多重路徑同步


```bash
#!/bin/bash

DSCORD_WEBHOOK="https://discordapp.com/api/webhooks/*"

function sendDiscordNotice(){
        local MSG=$2
        # echo $MSG
        local URL=$1
        curl -H "Content-Type: application/json" \
        -X POST \
        -d "{\"username\": \"system\", \"content\": \"${MSG}\"}" $URL
}

#sendDiscordNotice $DSCORD_WEBHOOK "test"

# 需要做同步的路徑，結尾資料夾不用/
LOCAL_SYNCDIR="/mnt/extHDD/Download"
# RCLONE 結尾不能有 /
RCLONE_SYNCDIR="gdrive:/test"
# 注意需要看.config/rclone 資料夾和檔案權限，需要到rx
RCLONE_CONFIG_PATH="--config=/home/pi/.config/rclone/rclone.conf"
AUTH="--auth admin:admin"
REPLATE_PATH=${TR_TORRENT_DIR#"${LOCAL_SYNCDIR}"/}  
#### debug end
df -H | grep -vE '^Filesystem|tmpfs|cdrom|udev|已用' | awk '{ print $5 " " $1 }' | while read output;
do
  usep=$(echo $output | awk '{ print $1}' | cut -d'%' -f1  )
  partition=$(echo $output | awk '{ print $2 }' )
  if [ $usep -ge 80 ]; then
    sendDiscordNotice $DSCORD_WEBHOOK "⚠️注意快超出硬碟空間\\\"$partition\\\" ($usep%) on $(hostname) as on $(date)"
  fi
done





if [[ "$TR_TORRENT_DIR" =~ ^"$LOCAL_SYNCDIR" ]]; then
    echo "pass"
    if [ -f "${TR_TORRENT_DIR}/${TR_TORRENT_NAME}" ]; then
        flock -x  /tmp/rclone.lock rclone copy ${RCLONE_CONFIG_PATH} "${TR_TORRENT_DIR}/${TR_TORRENT_NAME}" "${RCLONE_SYNCDIR}/${REPLATE_PATH}"
        rclone check ${RCLONE_CONFIG_PATH} "${TR_TORRENT_DIR}/${TR_TORRENT_NAME}" "${RCLONE_SYNCDIR}/${REPLATE_PATH}"
    else 
        flock -x  /tmp/rclone.lock rclone copy ${RCLONE_CONFIG_PATH} "${TR_TORRENT_DIR}/${TR_TORRENT_NAME}" "${RCLONE_SYNCDIR}/${REPLATE_PATH}/${TR_TORRENT_NAME}"
        rclone check ${RCLONE_CONFIG_PATH} "${TR_TORRENT_DIR}/${TR_TORRENT_NAME}" "$RCLONE_SYNCDIR/${REPLATE_PATH}/${TR_TORRENT_NAME}" --size-only
    fi
    
    if [ $? -eq 0 ]; then
        msg="✅ $TR_TORRENT_NAME 完成下載，並完成同步"
        echo $msg
        sendDiscordNotice $DSCORD_WEBHOOK "$msg"
        transmission-remote $AUTH -t $TR_TORRENT_ID --remove-and-delete
    else
        echo "FAIL"
        msg="⚠️ $TR_TORRENT_NAME 完成下載，但同步失敗"
        echo $msg
        sendDiscordNotice $DSCORD_WEBHOOK "$msg"
    fi
    
else
    msg="✅$TR_TORRENT_NAME 完成下載，不需要做同步"
    echo $msg
    sendDiscordNotice $DSCORD_WEBHOOK "$msg"
fi
```