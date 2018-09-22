---
title: GO編譯程式紀錄
date: 2018-09-01 02:04:50
tags: [GO]
categories: GO
---

最近玩golang最喜歡它編譯可以直接跨平台執行
而且看了一下
真的很簡單，只需要一行`go build`
但真的是這樣的嗎?
(不然也不會有這一篇了XD)

<!--more-->

golang編譯真的很簡單
只需要`go build main.go`即可以編譯`main.go`
但是`go build`是整個目錄都會進行編譯
編譯出來的程式正常`本機`可以執行

**為什麼要框住本機呢**

因為這就是我踩雷的地方
```
Mac 下编译 Linux 和 Windows 64位可执行程序
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build main.go
CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build main.go

Linux 下编译 Mac 和 Windows 64位可执行程序
CGO_ENABLED=0 GOOS=darwin GOARCH=amd64 go build main.go
CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build main.go

Windows 下编译 Mac 和 Linux 64位可执行程序
SET CGO_ENABLED=0
SET GOOS=darwin
SET GOARCH=amd64
go build main.go

SET CGO_ENABLED=0
SET GOOS=linux
SET GOARCH=amd64
go build main.go
```
參考來源:[Golang 在 Mac、Linux、Windows 下如何交叉编译 - CSDN博客](https://blog.csdn.net/panshiqu/article/details/53788067)
假如你是在window用git-bash話，前面變數可以加`export`
當然後面的`main.go`可以不用填

正常有`go get`套件編譯會沒有問題

環境變數設定完，可以`go env`查看GO環境變數設定值

但重點來了

## 編譯後別台電腦不能執行

奇怪我把程式丟到Linux竟然不能執行  (說好的編譯可以跑的  囧)
但發現執行錯誤訊息
竟然有出現我在Win7路徑
當下沒有留意到是抓不到Package問題
好像有甚麼**address**關鍵字錯誤
因為同事也有在玩
他說是抓不到套件關係

後來有爬到一個指令
在程式目錄執行
```
go build -a -v
```

這樣就很神奇可以跑
不過我在win7編譯Linux程式，在Linux還是不能跑
Linux編譯Win7程式，在Win7正常執行
但是在Win10編譯Linux程式，在Linux正持行
反之也可以

不知道Win7編譯GO怎麼了....

其實這篇重點只有`go build -v -a`
但網路上看到的文章真的不多
不知道為什麼...(有可能我方法不正確)

## gitea編譯紀錄
中間有試著`gitea`編譯
```
make generate all
```
不知道這一行我不能執行
但輸入`make`就正常運行
輸入`go build -v -a`也能跑，(`go build`好像不能)



參考來源
[golang - Go语言的三个命令 go build, go install, go get - SegmentFault 思否](https://segmentfault.com/q/1010000004044176)
[go build - GO 命令教程 - 极客学院Wiki](http://wiki.jikexueyuan.com/project/go-command-tutorial/0.1.html)