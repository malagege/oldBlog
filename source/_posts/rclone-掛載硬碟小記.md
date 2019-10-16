---
title: rclone 掛載硬碟小記
date: 2019-10-14 20:58:51
tags: [rclone]
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
5. sudo vim /etc/fstab  
6. 加入 UUID="F840-AAE1"  /mnt/extHDD vfat   rw,defaults 0 0 #(-a, --all 掛載 fstab 中的所有文件系統)



[Mount的用法詳細解析 - IT閱讀](https://www.itread01.com/content/1553540779.html)

最近看到[使 Amazon EBS 磁碟區可供在 Linux 上使用 - Amazon Elastic Compute Cloud](https://docs.aws.amazon.com/zh_tw/AWSEC2/latest/UserGuide/ebs-using-volumes.html)這篇
UUID=aebf131c-6957-451e-8d34-ec978d9581ae  /data  xfs  defaults,**nofail**  0  2
> 如果曾在不掛載此磁碟區的狀態下開機執行個體 (例如，在將磁碟區移至其他執行個體後)，nofail 掛載選項可讓執行個體繼續開機，即使在掛載磁碟區的作業出現錯誤。包括 16.04 前之 Ubuntu 版本在內的 Debian 衍生產品，也必須新增 nobootwait 掛載選項。 

/usr/bin/rclone mount --allow-root --vfs-cache-mode writes --vfs-cache-max-size 100M gdrive: /mnt/gdrive
2019/10/16 16:15:54 mount helper error: fusermount: option allow_root only allowed if 'user_allow_other' is set in /etc/fuse.conf

### tmux 掛載方式(不建議用)

在沒知道用 systemctl 原本想使用 tmux
不過看起來好像能跑，但我覺得 systemctl 比較完美

[tmux script example](https://gist.github.com/B-Galati/c7890f86fc2bfce0b98c5410d964727a)

```bash
#!/bin/bash
tmux new-window -d -n 'test' 'ping 127.0.0.1'
```

## tranmission 

[Transmission download complete shell script for moving complete files and removing its torrent entry](https://gist.github.com/awesometic/253b740d45f8e5f95b56ec24f33a9444)
[Script to clear finished torrents from transmission-daemon](https://gist.github.com/pawelszydlo/e2e1fc424f2c9d306f3a)
[Guide : Auto removal of downloads from transmission 2.82 - My Cloud - Personal Cloud Storage / My Cloud - WD Community](https://community.wd.com/t/guide-auto-removal-of-downloads-from-transmission-2-82/93156)
[Tmux 開啟後 自動 SSH 連結多台指定機器 | Tsung's Blog](https://blog.longwin.com.tw/2013/05/tmux-auto-ssh-remote-2013/)

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

