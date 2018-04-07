---
layout: post
title: 'CodeIgniter Session錯誤問題'
date: 2017-06-02 14:50
comments: true
categories: [CodeIgniter]
---
在config設定
```
$config['sess_save_path'] =BASEPATH.'../'.'session_file';
```
http://codeigniter.org.cn/forums/thread-21295-1-1.html
由於我是用Docker設定
目前猜測可能權限有問題...
導制錯誤
在此記錄，不然一下就忘記了XD
