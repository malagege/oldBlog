---
title: 使用 command line 上傳檔案到 HFS
date: 2020-07-15 23:17:32
tags: [linux,hfs,curl]
categories: Linux 
---

最近要使用 Linux 下載憑證下來看
主機沒有裝 vsftpd 想說用 WinSCP 下載
但又發現沒有使用 root 權限無法進去資料夾
有想到有沒有 sudo 方法
[Winscp使用sudo user登录-阿里云开发者社区](https://developer.aliyun.com/article/373923)
但不太想改這個設定...
最後突然想到有 Windows HFS 來接檔案
原本有想到 nc 接檔案，但好像很麻煩 哈哈
[Ubuntu 用 nc 指令互傳檔案](https://www.arthurtoday.com/2012/01/ubuntu.html)
Windows 也沒 nc 指令

<!--more-->


非常簡單

```bash
curl -F fileupload1=@filename -F press="Upload files" http://IP:Port/Dir/
```

記得資料夾要設定上傳 anyone
原理可以看[curl upload file @ 495884965 :: 痞客邦 ::](https://slinbody.pixnet.net/blog/post/66022614)


> #curl -v -F 'file=@/tmp/444.jpg' -b cookies.txt -c cookies.txt  https://your.web.site/upload
> 其中，file要看該網頁的變數名稱決定
>  
> 
> 例如： 
> ```html
>     <form method=post enctype=multipart/form-data>
>       <p><input type=file name=file_name>
>          <input type=submit value=Upload>
>     </form>
> ```
> 用curl時，就要變成file_name

我還以為 @ 是多餘的

最後成功

最後有爬到[upload with filename inside URL](http://rejetto.com/forum/index.php?pretty;board=hfs-~-http-file-server;topic=upload-with-filename-inside-url.msg1042258#msg1042258)

> 1) The curl command shown below without an ending "/" after the `<HFSfolder>` works better in that it return the "301" code to the console when everything works properly (with an ending "/" you get the echo of the HFS html upload page, the upload still works fine, but having the return code available to any error checking in a script is probably be better).  You probably know better than I why there is this slight difference between having the "/" at the end and not having it.
```bash
curl  -F fileupload1=@MyFile.iso  -F press="Upload files"  http://<HFSipaddress>/<HFSfolder>
```
> 2) I've tested curl ability to take input from stdin via a command, for example, like this:
```bash
cat testfile.gz  |  curl -F fileupload1=@-;filename=atestfile.gz -F press="Upload files" http://<HFSipaddress>/<HFSfolder>
```


## socat 大法

on host 1 	
```
socat -u open:myfile.exe,binary tcp-listen:999
```
on host 2 	
```
socat -u tcp:host1:999 open:myfile.exe,create,binary
```

參考:[socat 使用手册 « 倾旋的博客](https://payloads.online/tools/socat)