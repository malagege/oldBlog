---
title: go安裝筆記&gopath環境變數設定
date: 2018-08-27 00:20:03
tags: [go]
categories: GO
---

最近想玩玩看GO
在ElementaryOS安裝GO
踩幾個雷，紀錄一下筆記
這次的雷還深深了解以前code設定環境變數用途
<!--more-->

## apt-get 安裝GO
我比較懶，所以選擇這個方式安裝
用`apt-get install golang-go`
安裝完你會發現版本太舊
有些需要1.8版以上才能跑
`apt-get install golang-1.10-go`
記得這邊要加PATH環境變數
`export PATH="$PATH:/usr/lib/go-1.10/bin`
bash加在`~/.profile`或`~/.bashrc`
zsh加在`.zshrc`
之後有一篇會再講

參考來源
[Ubuntu · golang/go Wiki](https://github.com/golang/go/wiki/Ubuntu)



## GOPATH

沒有權限使用，可以改這個環境變數

>Go 1.8 is introducing a default GOPATH. If you don’t set one, the tools will use the default. Default GOPATH is:
>    $HOME/go on Unix-like systems
>    %USERPROFILE%\go on Windows
[The default GOPATH · Go, the unwritten parts](https://rakyll.org/default-gopath/)

原本以為會遇到這個問題
還有`GOROOT`可以設定GO目錄
不過我沒設定好像也能跑
所以就沒設定
詳細一些東西可以看裡面東西
[go环境变量配置 (GOROOT和GOPATH) - 简书](https://www.jianshu.com/p/4e699ff478a5)


## 其他程式語言兩三事

等等...，為什麼GOPATH就能解決權限問題
那...php,nodejs,沒有一樣設定的東西嗎

### java classpath
印象中...可以在環境變數先加classpath
不過這應該跟我們這篇沒關係
先跳過



### nodejs 設定套件golbal存放位置
`npm config get prefix`
可以抓到現在`npm install xxxx -g`存放路徑
Linux預設存放
`/usr/`
`C:\Users\(User)\AppData\Roaming\npm`

這邊我就很好奇，為什麼Linux可以執行`node`global為什麼可以在cmd上面打
原來那些路徑有設定到`PATH`
LINUX會在`/usr/bin`加程式位置
所以你可以打指令執行

所以你自訂`npm config set prefix ~/.npm-package`
加完~~不要高興太早~~記得要在`$PATH`加入你npm設定位置
不然cmd呼叫不到
[The Will Will Web | 如何在 Windows 平台變更 Node.js / npm 全域模組的預設安裝路徑](https://blog.miniasp.com/post/2015/09/01/Change-npm-default-global-installation-directory-for-nodejs-modules-in-Windows.aspx)


`npm install --prefix <path/to/prefix_folder> -g`好像能指定目錄安裝

[npm install --prefix: still available? · Issue #11007 · npm/npm](https://github.com/npm/npm/issues/11007)
[node.js - How to npm install to a specified directory? - Stack Overflow](https://stackoverflow.com/questions/14469515/how-to-npm-install-to-a-specified-directory)

更詳細介紹[npm的.npmrc文件在哪里？缓存及全局包文件在什么位置？](https://newsn.net/say/npm-whereis.html)
原來更改這些設定會寫入到`~/.npmrc`裡面


### php - composer  global

執行路徑會在composer 本目錄下
我沒有用過
先筆記
[php - Change composer global path (Windows) - Stack Overflow](https://stackoverflow.com/questions/25549177/change-composer-global-path-windows/25569354)
[php - Where to find composer's global packages? - Stack Overflow](https://stackoverflow.com/questions/30664220/where-to-find-composers-global-packages)

### python pip 

這個就爬得很久...
apt-get預設安裝在root上面
所以很大機率pip安裝都要加root

[python自定义安装python包－pip的使用-Bluesky's blog](http://yangl.net/2018/05/18/python-pip-usage/)

>用户目录下面，.pip目录下建立pip.conf文件，文件内容如下：
>[install]
>install-option=--prefix=~/.local
>然后pip的库就安装到用户目录下的.local下面了
[python自定义安装python包－pip的使用-Bluesky's blog](http://yangl.net/2018/05/18/python-pip-usage/)

這就沒有測試了...，奇怪這篇重點好像是GO
怎麼扯到別的程式語言呢XD


最後最近玩GO
他的package跟別的程式語言差好多
看來有空還是簡單記一下
[從 PHP 到 Golang 的筆記](https://yami.io/php-to-golang/)
