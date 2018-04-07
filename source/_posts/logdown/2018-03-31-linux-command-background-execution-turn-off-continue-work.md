---
layout: post
title: 'Linux [nohup]背景執行(command關掉繼續work)'
date: 2018-03-31 07:06
comments: true
categories: Linux
tags: [nohup,Linux]
---
最近有一台樹莓派網路比較不穩
想到另外一台做ping的動作
但是command line怎麼背景執行??

<!--more-->

# &
只要在command後面加&
就能背景執行
想不到這麼簡單
~~不過關掉command就會不見了~~
正確來說好像還會繼續執行樣子...([nohup和&的区别 - xiaohuazi - 博客园](http://www.cnblogs.com/MYSQLZOUQI/p/5283761.html))

# nohup ... &
在前面加入nohup
關掉command就會背景執行
會輸出到`nohup.out`


# nohup 跟  & 的指令差別
>sh 22.sh &
将任务放到后台 ，即使关闭xshell退出当前session依然继续运行，但标准输出和标准错误信息会丢失
nohup sh 22.sh
将任务放到后台，关闭标准输入，前台不再能够接收任何输入（标准输入），重定向标准输出和标准错误到当前目录下的nohup.out文件，即使关闭xshell退出当前session依然继续运行。
nohup sh 22.sh  &
将任务放到后台，但是依然可以使用标准输入，前台能够接收任何输入，重定向标准输出和标准错误到当前目录下的nohup.out文件，即使关闭xshell退出当前session依然继续运行。
nohup和&的缺点是，如果你要在一个shell会话里面执行多个命令和脚本，那么要每个命令和脚本都要加nohup和&非常麻烦，所以才有了screen和TMUX

[nohup和&的区别 - xiaohuazi - 博客园](http://www.cnblogs.com/MYSQLZOUQI/p/5283761.html)

`$ nohup your_command &>myout.txt &`
可以重新導向新的輸出
但注意`&>`是連在一起的，不能分開

看到這篇...，突然想到screen跟tmux
還沒記筆記，不過也忘了一半
改天再補...

發現Linux 做ping動作不會有時間...
[Linux/Windows下Ping测试加上返回时间戳的方法 - 彩荷z](http://blog.caihez.com/?p=716)
[Linux下长时间ping网络加时间戳并记录到文本 - CSDN博客](https://blog.csdn.net/gtlions/article/details/7974087)





參考來源
* [[linux[tips]nohup: 讓command在遠端執行，不因登出而中斷 @ 真實旅程 :: 痞客邦 ::](http://dragonspring.pixnet.net/blog/post/33257496-%5Blinux%5Btips%5Dnohup:-%E8%AE%93command%E5%9C%A8%E9%81%A0%E7%AB%AF%E5%9F%B7%E8%A1%8C%EF%BC%8C%E4%B8%8D%E5%9B%A0%E7%99%BB%E5%87%BA)
* [nohup和&的区别 - xiaohuazi - 博客园](http://www.cnblogs.com/MYSQLZOUQI/p/5283761.html)
* [Linux 學習日誌: 把命令放到背景執行](http://linuxdiary.blogspot.com/2007/10/blog-post_30.html)
* [鳥哥的 Linux 私房菜 -- Linux 常用網路指令介紹](http://linux.vbird.org/linux_server/0140networkcommand.php#ifup)
* [追蹤連線區域網路裝置的Raspberry Pi掃描器 - Make 國際中文版 > DIY Projects, Inspiration, How-tos, Hacks, Mods & More @ Makezine.com.tw - Tweak Technology to Your Will](http://www.makezine.com.tw/make2599131456/raspberry-pi8273188)
* [Linux 學習日誌: 把命令放到背景執行](http://linuxdiary.blogspot.com/2007/10/blog-post_30.html)
* [nohup和&後臺執行，程序檢視及終止 - 掃文資訊](https://tw.saowen.com/a/63aedd5ffa61bd61c20a8e25445d1f9aba8f4c24d4920b480460fba0a7625353)