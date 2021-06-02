---
title: html5-qrcode 簡單研究
date: 2021-06-02 20:53:57
tags: [html5,javascript,qrcode]
categories: JavaScript
---

最近[疾管家](https://cdc-web.deepq.app/vsafe/scanner?liffId=1655719655-jmaeX5gA)
QRCOD掃描功能出來
公司叫我查看怎麼做出這個案例
簡單看了一下
[1922 簡訊實聯制 - HackMD](https://g0v.hackmd.io/@au/HkmyoS-Fu)

<!--more-->


竟然是 html5-qrcode
想看他的原始碼，~~竟然找不到~~
後來看原始碼，有看到map設定

突然想到這個好像能看原始碼
[保哥 - 前端工程師不可不知的 Source Maps 應用技巧](https://www.facebook.com/will.fans/videos/1834092806619855)

### html5-qrcode 簡單探討

取得相機功能
預防以後API會變
這邊主要連結到官方文件

另外，需要`https`協定才能開攝像頭

假如你有架設traefik+docker
可以用以下指令快速建制

```bash=
docker run -d --rm  --name my-apache-app  -v "$PWD":/usr/local/apache2/htdocs/     -l  "traefik.http.routers.testweb.rule=Host(\`testweb.xxxx.duckdns.org\`)"  -l "traefik.http.routers.testweb.tls=true"  --net traefik_network  httpd:2.4

```


#### 抓取設備


[GitHub - mebjas/html5-qrcode: A cross platform HTML5 QR code reader](https://github.com/mebjas/html5-qrcode#pro-mode---if-you-want-to-implement-your-own-user-interface)

device id 最後一個，為後鏡頭
後來發現，不需要這樣抓後鏡頭

#### 設定後鏡頭

```javascript=

// If you want to prefer back camera
html5QrCode.start({ facingMode: "environment" }, config, qrCodeSuccessCallback);
```

[GitHub - mebjas/html5-qrcode: A cross platform HTML5 QR code reader](https://github.com/mebjas/html5-qrcode#pro-mode---if-you-want-to-implement-your-own-user-interface)

#### 開始掃qrcode

```javascript=
const html5QrCode = new Html5Qrcode(/* element id */ "reader");
html5QrCode.start(
  cameraId, 
  {
    fps: 10,    // Optional frame per seconds for qr code scanning
    qrbox: 250  // Optional if you want bounded box UI
  },
  qrCodeMessage => {
    // do something when code is read
  },
  errorMessage => {
    // parse error, ignore it.
  })
.catch(err => {
  // Start failed, handle it.
});

```

#### 暫停掃 qrcode

[GitHub - mebjas/html5-qrcode: A cross platform HTML5 QR code reader](https://github.com/mebjas/html5-qrcode#stop-scanning)

```javascript=
html5QrCode.stop().then(ignore => {
  // QR Code scanning is stopped.
}).catch(err => {
  // Stop failed, handle it.
});

```

#### 地雷(記得掃到馬上停掉)

記得掃到馬上停掉!!
記得掃到馬上停掉!!
記得掃到馬上停掉!!

因為掃到，持續會callback 開簡訊
會悲劇....

掃到，stopcamera 就比較不會出問題



### js 轉簡訊

這邊有個大地雷 qrcode 轉 sms 程式
Android 竟然不能轉
但是IOS可以

這邊就是使用
```javascript 
    if (isAndroid()) {
        location.href = "sms:1922?body=00000000"
    } else {
        // iOS
        location.href = "sms:1922&body=00000000"
    }

```
Android用超連結就可順利執行

為什麼liff Android 可以直接轉的?目前猜測是line app可以這樣做
把疾管家用到 Chrome 就不能使用
這邊就不深入了


####  網路範例
[bradorego.com/test/sms.html](https://bradorego.com/test/sms.html)

```htmlembedded=
	<p>
			<a href="sms:5552345678">Phone only</a>
		</p>
		<p>
			<a href="sms:+15552345678">Phone(+1) only</a>
		</p>
		<p>
			<a href="sms:?body=Hello,%20world">Body only</a>
		</p>
		<p>
			<a href="sms:;body=Hello,%20world">;body only</a>
		</p>
		<p>
			<a href="sms:5552345678?body=Hello%20World">Phone and ?body</a>
		</p>
		<p>
			<a href="sms:+15552345678?body=Hello%20World">Phone(+1) and ?body</a>
		</p>
		<p>
			<a href="sms:5552345678;body=Hello%20World">Phone(+1) and ;body</a>
		</p>
		<p>
			<a href="sms://5552345678">Phone only (sms://)</a>
		</p>
		<p>
			<a href="sms://+15552345678">Phone(+1) only (sms://)</a>
		</p>
		<p>
			<a href="sms://5552345678?body=Hello,%20World">Phone and ?body (sms://)</a>
		</p>
		<p>
			<a href="sms://+15552345678?body=Hello,%20World">Phone(+1) and ?body (sms://)</a>
		</p>
		<p>
			<a href="sms://5552345678;body=Hello,%20World">Phone and ;body (sms://)</a>
		</p>
		<p>
			<a href="sms://+15552345678;body=Hello,%20World">Phone(+1) and ;body (sms://)</a>
		</p>
```
### SMSTO

這邊有個地雷，SMSTO 小米手機不能轉
可以用Google 智慧鏡頭 解決

QRCODE 

正常手機掃條碼就能自動轉


## 彩蛋

[vite2: Make Web Dev Fast Again & Again. – Dropbox Paper](https://paper.dropbox.com/doc/vite2-Make-Web-Dev-Fast-Again-Again.-9DcJOLb0spaYNbCbF6Zqd)

[ES6,7,8,9,10,11中，用一次就上[GitHub - kurotanshi/hex-vite-demo: 2021 大神來六角 demo code](https://github.com/kurotanshi/hex-vite-demo)癮的語法 – Dropbox Paper](https://paper.dropbox.com/doc/ES67891011-oUCvWdcmTSKsiuGzbTjZC)


