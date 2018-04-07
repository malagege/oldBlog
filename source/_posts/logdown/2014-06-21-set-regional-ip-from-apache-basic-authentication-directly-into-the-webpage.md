---
layout: post
title: 'Apache設定區域IP免BASIC認證就直接進入網頁'
date: 2014-06-21 16:22
comments: true
categories: Apache
tags: [Http Auth,Apache,.htaccess]
---
最近厭煩在家還要輸入Basic認證才能線上聽到我的Raspberry Pi上音樂
看了大多數htaccess設定  都沒辦法達成我想要的結果

最後在[.htaccess - Allow IP address without authentication - Stack Overflow](http://stackoverflow.com/questions/3649852/allow-ip-address-without-authentication)這篇找到正解
<!--more-->


```
Order deny,allow
Deny from all
AuthName "Please type password"
AuthType Basic
AuthUserFile "/var/www/player/.htpasswd"
require valid-user
Allow from 192.168.0.0/24
Satisfy Any

```

以後在家就不用打認證:D

htaccess相關設定文件
======
[在線上製作.htaccess](http://www.htaccesseditor.com/tc.shtml)
[Apache的Satisfy详解](http://www.fwolf.com/blog/post/341)
[常見的 .htaccess 使用技巧](http://www.php100.com/html/webkaifa/apache/2012/1031/11436.html)
