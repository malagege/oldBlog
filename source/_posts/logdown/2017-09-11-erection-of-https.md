---
layout: post
title: 'Raspberry PI 自動設定Let’s Encrypt！ TLS(SSL 憑證)'
date: 2017-09-11 13:09
comments: true
categories: Linux
tags: [Linux,TLS,SSL,Let’s Encrypt,Certbot]
---
 最近想讓動物機增加安全性，有看到免費的letsencrypt教學文
 又好像有工具自動產生憑證
 以經有看到勇者試了，當然也要嘗試:P


<!--more-->


本來想說應該會很順利吧
結果沒有...

[申請Let’s Encrypt－以Raspberry Pi為例 ](https://www.weblink.idv.tw/2017/74)，
再做這篇的時候會跳出版本太舊

`sudo ./certbot-auto --apache --domain=**網域** certonly`

動物機好像說版本太舊

```
$ sudo apt-get remove libaugeas0 augeas-tools libaugeas-dev
$ sudo apt-get install libreadline6-dev libxml2-dev
$ cd /tmp
$ wget http://download.augeas.net/augeas-1.6.0.tar.gz
$ tar -zxf augeas-1.6.0.tar.gz
$ cd augeas-1.6.0
$ CFLAGS='-march=armv6 -mfpu=vfp -mfloat-abi=hard' CPPFLAGS='-march=armv6 -mfpu=vfp -mfloat-abi=hard' ./configure
$ CFLAGS='-march=armv6 -mfpu=vfp -mfloat-abi=hard' CPPFLAGS='-march=armv6 -mfpu=vfp -mfloat-abi=hard' make
$ sudo make install
```
[Let’s Encrypt for the Raspberry Pi (model B)](https://lordofthemoon.com/blog/2016/08/lets-encrypt-for-the-raspberry-pi-model-b/)
後來找上面那個方法還是沒用
最後~~賭~~升級`sudo apt-get dist-uprgrade`
升上去就可以跑了...

但會出現Timeout狀態
(之後有找到原因我的AP的 Virtual Server沒有設定443PORT)
不過我最後設完，apache設定沒有自動增加ssl設定

看來有機會還是要開一個apache筆記



最後看到另一篇用webroot方法，[免費的 SSL 憑證：Let’s Encrypt！](https://ezbox.idv.tw/52/free-ssl-lets-encrypt-certbot/)

`sudo ./certbot-auto certonly --webroot -d ***網域***`

 ```
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator webroot, Installer None
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for ****

Select the webroot for ****
-------------------------------------------------------------------------------
1: Enter a new webroot
-------------------------------------------------------------------------------
Press 1 [enter] to confirm the selection (press 'c' to cancel):

** Invalid input **
Press 1 [enter] to confirm the selection (press 'c' to cancel): 1
Input the webroot for ******: (Enter 'c' to cancel):

-------------------------------------------------------------------------------
 does not exist or is not a directory
-------------------------------------------------------------------------------
Input the webroot for ********: (Enter 'c' to cancel): /var/www/
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/****/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/****/privkey.pem
   Your cert will expire on 2017-12-10. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot-auto
   again. To non-interactively renew *all* of your certificates, run
   "certbot-auto renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

看到這個就表示成功
>  申請的網域的SSL憑證
>cert.pem: Your domain's certificate
 Let's Encrypt 的 鏈證書
>chain.pem: The Let's Encrypt chain certificate
 cert.pem 和 chain.pem 的合併檔
>fullchain.pem: cert.pem and chain.pem combined
 SSL憑證的私鑰，千千萬萬要飽護好
>privkey.pem: Your certificate's private key

```
<VirtualHost *:443>
        ServerAdmin service@**網域**
        DocumentRoot /var/www/
        ServerName **網域**
        ServerAlias **網域**

        SSLEngine On
        SSLCertificateFile /etc/letsencrypt/live/**網域**/cert.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/**網域**/privkey.pem
        SSLCertificateChainFile /etc/letsencrypt/live/**網域**/chain.pem
        SSLCACertificateFile /etc/letsencrypt/live/**網域**/fullchain.pem
</VirtualHost>
```


`Invalid command 'SSLEngine', perhaps misspelled or defined by a module not included in the server configuration`
這個錯誤訊息是  沒有啟用ssl模組
啟用
`sudo a2dismod ssl`

但後來發現怎麼很多沒有教關閉
爬文一下就找到了

關閉
`sudo a2enmod ssl`



因為先前發現不能連webroot可以產生
但還是連不到443port
後來發現是Virtual Server沒設
設定完OK
想說試試第一個方法跑一次
結果跑出另一個憑證

爬文找刪除
直接刪資料夾是不能解決問題的XD

# 回收憑證

　　`certbot revoke --cert-path /etc/letsencrypt/live/example.com/cert.pem`

　　`certbot delete --cert-name example.com`




Ubuntu 12.04啟用apache的 SSL
http://kirby86a.pixnet.net/blog/post/95136568-ubuntu-12.04%E5%95%9F%E7%94%A8apache%E7%9A%84-ssl
免費的 SSL 憑證：Let’s Encrypt！
https://ezbox.idv.tw/52/free-ssl-lets-encrypt-certbot/


番外apache mod rewrite功能

`sudo a2enmod rewrite`

or

` sudo ln -s /etc/apache2/mods-available/rewrite.load /etc/apache2/mods-enabled/rewrite.load`

在/etc/apache2/sites-enabled/000-default

` AllowOverride None`

```
RewriteEngine on
RewriteCond  %{HTTPS} !=on
#RewriteRule  ^(.*) https://%{SERVER_NAME}$1 [L,R]
 RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R]

```

這樣http就能導到https了

我有擋國外IP要怎麼自動更新憑證

```ssl_renew.sh
iptables -F
/usr/local/bin/certbot-auto renew --quiet
sh /etc/network/iptables.sh
```

自動更新憑證
`30 2 1 * * sh /root/ssl_bot.sh`


**2017/10/17**
發現網站憑證日期怎麼沒有更新...
以為設定有問題
Google查了一下
>這是因為certbot-auto renew 會讀取 /etc/letsencrypt/renewal 下的設定
檢查每個授權是否距離過期時間只剩30天，如果是才執行更新
( 參考：https://tiicle.com/items/15/ubuntu-1404-using-the-let-s-configuration-is-security-to-encrypt-nginx-https-server )

待下個月再檢查

參考來源: https://www.tipga.com/e/597853093286fe2b8f7a56e5





---
在RT1900AC上使用Let's Encrypt的SSL憑證-半殘版
https://darkdragon-darkdragon.blogspot.com/2017/05/rt1900aclets-encryptssl.html
NGINX 使用 Let’s Encrypt 免費 SSL 憑證設定 HTTPS 安全加密網頁教學
https://blog.gtwang.org/linux/secure-nginx-with-lets-encrypt-ssl-certificate-on-ubuntu-and-debian/

啟動 Ubuntu 下的 Apache mod rewrite 與相關設定
http://asika.windspeaker.co/post/3471-start-with-ubuntu-apache-the-mod-the-rewrite-set




