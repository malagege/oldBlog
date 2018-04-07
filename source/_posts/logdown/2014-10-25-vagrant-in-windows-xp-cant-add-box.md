---
layout: post
title: '在Windows XP上vagrant不能加box'
date: 2014-10-25 16:51
comments: true
categories: vagrant
---
學校給我一台舊電腦(CPU:Sempron 64 3100+ 1.8Ghz，RAM:512MB)
於是我買了二手RAM:1G*2 300塊含運
電腦就順多了:D
<!--more-->

![com.JPG](http://user-image.logdown.io/user/8284/blog/8171/post/239862/fZVIUs79SzW4wZnYhPeS_com.JPG)
由於灌ezgo11顯卡驅動沒有抓到via sig unichrome pro
google一大堆都沒有解決

所以就試試XP下裝Vagrant，還是遇到問題Orz
`The box failed to unpackage properly.`
Google搜尋了一下，最終找到解決[Error while adding Vagrant box in windows xp](https://github.com/mitchellh/vagrant/issues/3869)

>my solution was to download the basic_bsdtar.exe from here http://sourceforge.net/projects/mingw/files/MinGW/Extension/bsdtar/ and rename it to bsdtar.exe and place it in C:\HashiCorp\Vagrant\embedded\gnuwin32\bin\

1. 去http://sourceforge.net/projects/mingw/files/MinGW/Extension/bsdtar/ 下載檔案
2. 到C:\HashiCorp\Vagrant\embedded\gnuwin32\bin\ 把bsdtar.exe取代掉
3. vagrant即可加入box

