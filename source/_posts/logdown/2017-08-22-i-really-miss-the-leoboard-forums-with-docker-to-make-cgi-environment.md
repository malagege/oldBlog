---
layout: post
title: '好懷念leoboard論壇，用docker動手做cgi環境(未完成)'
date: 2017-08-22 13:20
comments: true
categories: Docker
tags: [Docker]
---
```
<IfModule !mpm_prefork_module>

        LoadModule cgid_module modules/mod_cgid.so

</IfModule>

<IfModule mpm_prefork_module>

        LoadModule cgi_module modules/mod_cgi.so

</IfModule>
```
```
ScriptAlias /cgi-bin/ "/usr/local/apache/cgi-bin/" apache
```


```
[Wed Mar 19 15:40:31.179155 2014] [cgid:error] [pid 4796:tid 140208841959296] (13)Permission denied: AH01241: exec of '/var/cgi-bin/echo.pl' failed
```
`chmod +x /var/cgi-bin/echo.pl`

http://nohup.cc/article/206/
http://www.cnblogs.com/phpgo/p/5669818.html
https://stackoverflow.com/questions/18392741/apache2-ah01630-client-denied-by-server-configuration