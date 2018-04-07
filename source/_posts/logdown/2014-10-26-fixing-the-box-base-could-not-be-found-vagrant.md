---
layout: post
title: 'Fixing “The box ‘base’ could not be found.” (Vagrant)'
date: 2014-10-26 12:24
comments: true
categories: vagrant
---
vagrant box add base http://files.vagrantup.com/lucid32.box

>This is an indication that the the base virtual image is not configured, usually from forgetting to finish the installation. This image is copied, and modified to produce the desired virtualized environment. It’s easy to fix, by running the following:
>
`vagrant box add base http://files.vagrantup.com/lucid32.box`
>
It is also possible that you installed the box under a different name (the Vagrant website sometimes refers to lucid32). Instead of downloading a new box, in this case, you can edit Vagrantfile like so, changing the box name to whatever you called it on download:
>
   `config.vm.box = "lucid32"`

>If you don’t know what you called it:
>
`vagrant box list`

http://www.garysieling.com/blog/fixing-the-box-base-could-not-be-found-vagrant

------------------------------
題外話，

由於我這台XP上`vagrant up`vagrant不能用share folder
找不到解決方法

結果後來發現是我腦殘把key改掉
刪掉vagrant_home(.vagrant資料夾)
就解決了