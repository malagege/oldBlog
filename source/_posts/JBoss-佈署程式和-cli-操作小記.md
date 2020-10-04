---
title: JBoss 佈署程式和 cli 操作小記
date: 2020-09-29 20:59:04
tags: [jboss]
categories: Java
---

最近公司佈署 war 檔程式
總是常常遇到 OOM(Out of Memory) 狀況
這時候有幾個佈署必須要想辦法排除
這邊只記錄我排除經驗與猜測

<!--more-->

首先，我先講我認知的佈署 war 兩個方法
在 `$JBOSS_HOME/standalone/deployment` 裡面放置 war 檔案就可以佈署
另一個方法是用 SE 會用一個方法佈署到 JBoss 裡面
但是 `$JBOSS_HOME/standalone/deployment` 不會看到 war 檔

## 手動重新佈署

假如上的 war 沒有動作

touch jboss-helloworld.war.dodeploy

可以這樣做

## 異常事件重新佈署上到舊程式

首先，我專案用的佈署 war 是 Jenkins
因為是 SE 關係，所以我沒辦法看到他設定怎麼佈署
但有時候佈署程式，需要手動上 war。

最近發生怪事情，就是有時候遇到 OOM ， restart JBoss 會 failed
然後我把 JBoss 的 `$JBOSS_HOME/standalone/config/standalone.xml`
裡面 deployment tag 我上失敗 war 清掉
舊可以正常打開，但是需要重新上 war 檔

但最近不知道 SE ，他們 Jenkns 有調整?
我 `$JBOSS_HOME/standalone/config/standalone.xml`

### 異常事件原因

最近剛好看到 JBoss-cli 這個功能，他可以遠端(Remote)佈署 war
感覺 SE 是用這個方式佈署 war

首先遠端佈署你的 JBoss Server 就要啟動(port 9990)

> 難怪之前 stop JBoss Server ，用 Jenkns 佈署會失敗...

在 local 端使用 $JBOSS_HOME/bin/jboss-cli.sh
進去會看到類似 cli 指令可以輸入

connect xx.xx.xx.xx 就可以進去(沒有設定密碼，會看到 cli 介面)

這時候就可以輸入 deploy xxxx.war(local) 就可以佈署 ~~其實我還沒有機會嘗試~~

## 這邊疑問

$JBOSS_HOME/standalone/config/standalone.xml

和

jboss-cli deploy 

佈署會不會衝突? 這邊是我的猜測
然後 $JBOSS_HOME/standalone/config/standalone.xml 裡面的 deployment
可以看到 cli 佈署出來 war tag

其實我還沒搞清楚熱佈署是什麼
$JBOSS_HOME/standalone/deployment 其實我在懷疑這邊是不是熱佈署?

為什麼正式環境改用這jboss-cli 佈署呢?

## jboss-cli 指令

`deployment-info `
NAME                 RUNTIME-NAME         PERSISTENT ENABLED STATUS
jboss-ejb-in-ear.ear jboss-ejb-in-ear.ear false       true    OK
singleton_in_war.war singleton_in_war.war true       true    OK

其中的 PERSISTENT 並沒查到是什麼意思
目前猜測是重啟 war 是不是存在
因為 deployment 重啟 war 檔案會重新 load 檔案樣子?

### 嘗試過指令

查詢目前啟動 war 狀況

/deployment=*:read-attribute(name=name)

這個超像 war XPath 查詢
參考: [command line interface - JBoss 7 CLI to query all the deployed applications - Stack Overflow](https://stackoverflow.com/questions/21928887/jboss-7-cli-to-query-all-the-deployed-applications)

### 其他相關指令連結

* [Jboss 四種部署方法以及探討 - IT閱讀](https://www.itread01.com/p/566472.html)
* [jboss CLI 命令行接口学习（适用JBOSS EAP 6.2+） - 菩提树下的杨过 - 博客园](https://www.cnblogs.com/yjmyzz/p/4754976.html) 詳細
* [Jboss热部署--jboss-cli.sh指令方式_A HUGE FATTY-CSDN博客](https://blog.csdn.net/t694728793/article/details/99698846) 詳細
* [JEAP 6 - cli 筆記](http://wei-meilin.blogspot.com/2012/11/jeap-6-cli.html)
* [[JBoss] JBoss Deploy 讀取資料夾方式, Deploy exploded archives JBoss EAP 6 @ 瑞先生 :: 痞客邦 ::](https://luckyboy7527.pixnet.net/blog/post/116777056-%5Bjboss%5D-jboss-deploy-%E8%AE%80%E5%8F%96%E8%B3%87%E6%96%99%E5%A4%BE%E6%96%B9%E5%BC%8F%2C-deploy-exploded-) dodeploy
* [command line interface - JBoss 7 CLI to query all the deployed applications - Stack Overflow](https://stackoverflow.com/questions/21928887/jboss-7-cli-to-query-all-the-deployed-applications)
* [CLI command to check deployment status in JBoss EAP - Red Hat Customer Portal](https://access.redhat.com/solutions/383573)

## OOM

- [【原创】一个线程oom，进程里其他线程还能运行吗？ - 孤独烟 - 博客园](https://www.cnblogs.com/rjzheng/p/9685622.html)

- [线程和进程的区别是什么？ - 知乎](https://www.zhihu.com/question/25532384)
> 進程是資源分配的最小單位，線程是CPU調度的最小單位


彩蛋

* [从 NextCloud 替换为 Seafile | zwPapEr](https://page.codespaper.com/2020/from-nextcloud-to-seafile/)
* [syncthing/syncthing: Open Source Continuous File Synchronization](https://github.com/syncthing/syncthing)
* [抛弃nextcloud,开始搭建使用cozycloud](https://shingle.me/post/%E6%8A%9B%E5%BC%83nextcloud%E5%BC%80%E5%A7%8B%E6%90%AD%E5%BB%BA%E4%BD%BF%E7%94%A8cozycloud/)
這網站有趣，切換出去會跑出 `(=・ω・=) 页面崩溃啦 ~ 快回来看看~ `
有空來研究怎麼搞出這個XD

我有時間寫的話，計畫要寫 seafile docker 實作
[Clementine 音樂播放器](https://www.clementine-player.org/zh_TW/)
[树莓派部署 seafile 和 nextcloud 比较 - 知乎](https://zhuanlan.zhihu.com/p/140289336)
[domenukk/seafile-docker-pi: A Docker image for Seafile server - Raspberry Pi Edition](https://github.com/domenukk/seafile-docker-pi)