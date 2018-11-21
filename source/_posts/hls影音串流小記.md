---
title: hls影音串流小記
date: 2018-11-13 21:50:22
tags: [hls,ffmpeg]
categories: HTML
---

最近看到網頁上的影音，他播放的時候不會全下載
好奇就網頁爬了一下
是用hls技術，雖然現在還沒有用到
不過我還滿感一點興趣
就查一些記錄，筆記筆記

<!--more-->

* [M3U8 串流影音 - 概念 與 下載 - NotFalse 技術客](https://notfalse.net/63/m3u8-intro)
* [流媒体：ffmpeg生成HLS的m3u8与ts片段 - 小雨伞漂流记 - 开源中国](https://my.oschina.net/ososchina/blog/828100)
* [ffmpeg常用基本命令(转) - wainiwann - 博客园](http://www.cnblogs.com/wainiwann/p/4128154.html)

切割hls檔案
`ffmpeg -i low_30.mp3 -c:a aac -b:a 64k -vn -hls_list_size 0 abc.m3u8`
`ffmpeg -i eq_1.mp4 -vcodec copy -acodec copy ~~-vbsf h264_mp4toannexb~~ -hls_time 10 -hls_list_size 999999999 output.m3u8`
`-vbsf h264_mp4toannexb` 拿掉就可以跑了
雖然不知道甚麼問題，不過解決了

* [Only one .ts file is generating while encoding mp3 file to m3u8 using ffmpeg - Stack Overflow](https://stackoverflow.com/questions/45367762/only-one-ts-file-is-generating-while-encoding-mp3-file-to-m3u8-using-ffmpeg)
* [ffmpeg HTTP Live Streaming](https://jonlabelle.com/snippets/view/shell/ffmpeg-http-live-streaming)


* [Albert 的筆記本: HLS (HTTP Live streams) 介紹](http://albert-oma.blogspot.com/2016/02/hls-http-live-streams.html)
* [巴哈動畫瘋 跳過網站直抓 m3u8 (更新版 – 柏狗屁世界](https://bgpsekai.thisistap.com/%E9%9A%A8%E6%89%8B%E4%BA%82%E5%81%9A/2017/02/%E5%B7%B4%E5%93%88%E5%8B%95%E7%95%AB%E7%98%8B-%E8%B7%B3%E9%81%8E%E7%B6%B2%E7%AB%99%E7%9B%B4%E6%8A%93-m3u8-%E6%9B%B4%E6%96%B0/)
* [SRS中增加身份验证 - dlbtsoft - 博客园](https://www.cnblogs.com/hrybird/p/5125182.html)
* [srs 身份认证«海底苍鹰(tank)博客](http://blog.51yip.com/server/1822.html)
* [樹莓派架設 RTMP 串流（Streaming）伺服器，傳送即時攝影機影像 - G. T. Wang](https://blog.gtwang.org/iot/raspberry-pi-nginx-rtmp-server-live-streaming/)
* [v2_CN_Home · ossrs/srs Wiki](https://github.com/ossrs/srs/wiki/v2_CN_Home)
* [Directives · arut/nginx-rtmp-module Wiki](https://github.com/arut/nginx-rtmp-module/wiki/Directives)


* [重启php7-fpm和php5-fpm方法大全 - 歪麦博客](https://www.awaimai.com/2391.html)
* [三种主流流媒体协议(RTMP，RTSP，HTTP)比较 - 流媒体技术 - AMS- 为天下企业提供流媒体软件引擎！](http://www.avpk.cn/article/131.html)
* [How can I display an RTSP video stream in a web page? - Stack Overflow](https://stackoverflow.com/questions/2245040/how-can-i-display-an-rtsp-video-stream-in-a-web-page)
* [流媒体之RTSP/RTP/RTCP | Winddoing's Blog](https://winddoing.github.io/post/34052.html)
* [Setting up HLS live streaming server using NGINX - Peer5 Docs](https://docs.peer5.com/guides/setting-up-hls-live-streaming-server-using-nginx/)
* [srs 身份认证«海底苍鹰(tank)博客](http://blog.51yip.com/server/1822.html)
* [srs流媒体服务器 安装配置«海底苍鹰(tank)博客](http://blog.51yip.com/server/1815.html)
* [ffmpeg处理RTMP流媒体的命令大全 - 雷霄骅(leixiaohua1020)的专栏 - CSDN博客](https://blog.csdn.net/leixiaohua1020/article/details/12029543)
* [ffmpeg處理RTMP流媒體的命令和傳送流媒體的命令（UDP，RTP，RTMP） - 掃文資訊](https://tw.saowen.com/a/c003e14c9686e3a151be9203a48ccf662cff5ec90e5437e9e8b0c0c51e266371)

[Nginx-rtmp模块实现流媒体play、push、pull功能-Dream come true-51CTO博客](http://blog.51cto.com/lyd0721/1429803)
[直播技术实现(一)](http://www.dengshenyu.com/%E5%90%8E%E7%AB%AF%E6%8A%80%E6%9C%AF/2016/09/05/live-streaming.html)
[直播技术实现(二)](http://www.dengshenyu.com/%E5%90%8E%E7%AB%AF%E6%8A%80%E6%9C%AF/2016/09/05/live-streaming-2.html)
[筆記：使用 nginx 搭建一個 HLS(HTTP Live Streaming) & Rtmp 直播服務器 | 奈特的魔法科研](https://nat.moe/technologies/yinfb/973/)


名詞簡單小記
#### SRS(Simple RTMP Server)
架設RTMP Server
簡單來說就是`直播伺服器`
> 3、将其中一个直播流，视频改用h264压缩，音频不变，送至另外一个直播服务流
`ffmpeg -i rtmp://server/live/originalStream -c:a copy -c:v libx264 -vpre slow -f flv rtmp://server/live/h264Stream`
當出看一直看不到為什麼rtmp傳到另一個rtmp
後來看到`直播服務流`
就想到應該像youtube,twitter
讓我想到obs應該也是發到rtmp server去

#### rtmp rstp hls(http live streaming)
* [三种主流流媒体协议(RTMP，RTSP，HTTP)比较 - 流媒体技术 - AMS- 为天下企业提供流媒体软件引擎！](http://www.avpk.cn/article/131.html)
```
1、HTTP協議:
 HTTP的視頻協議，主要是在互聯網普及之後。在互聯網上看視頻的需求下形成的。
   最初的HTTP視頻協議，沒有任何特別之處，就是通用的HTTP文件漸進式下載。本質就是下載視頻文件，而利用視頻文件本身的特點，就是存在頭部信息，和部分視頻幀數據，就完全可以解碼播放了。顯然這種方式需要將視頻文件的頭部信息放在文件的前面。有些例如faststart工具，就是專門做這個功能的。
    但是最為原始的狀態下，視頻無法進行快進或者跳轉播放到文件尚未被下載到的部分。這個時候對HTTP協議提出了range-request的要求。這個目前幾乎所有HTTP的服務器都支持了。range-request，是請求文件的部分數據，指定偏移字節數。在視頻客戶端解析出視頻文件的頭部後，就可以判斷後續視頻相應的幀的位置了。或者根據碼率等信息，計算相應的為位置。
 
優點：
HTTP Live Streaming 還有一個巨大優勢：自適應碼率流播（adaptive streaming）。效果就是客戶端會根據網絡狀況自動選擇不同碼率的視頻流，條件允許的情況下使用高碼率，網絡繁忙的時候使用低碼率，並且自動在二者間隨意切換。這對移動設備網絡狀況不穩定的情況下保障流暢播放非常有幫助。實現方法是服務器端提供多碼率視頻流，並且在列表文件中註明，播放器根據播放進度和下載速度自動調整。使用起來也非常簡單。
缺點：
    實時性相對較差，直播的時候延遲比較高。當然，現在進化出來的flv_over_http或者ts_over_http也可以做到直播延時很低，基本和rtmp協議差不多。
  
2、RTSP協議：
用於Internet上針對多媒體數據流的一種傳輸協議，是TCP/IP協議體系中的一個應用層協議，RTSP在體系結構上位於RTP和RTCP之上，它使用TCP或UDP完成數據傳輸，該協議定義了一對多應用程序如何有效地通過IP網絡傳送多媒體數據。
   本協議是最早的視頻傳輸協議。其中RTSP協議用於視頻點播的會話控制，例如發起點播請求的SETUP請求，進行具體播放操作的PLAY、PAUSE請求，視頻的跳轉也是通過PLAY請求的參數支持的。
 
優點：
RTSP協議族的優勢，在於可以控制到視頻幀，因此可以承載實時性很高的應用。這個優點是相對於HTTP方式的最大優點。H.323視頻會議協議，底層一般採用RTSP協議。RTSP協議族的複雜度主要集中在服務器端，因為服務器端需要parse視頻文件，seek到具體的視頻幀，而且可能還需要進行倍速播放（就是老舊的DVD帶的那種2倍速，4倍速播放的功能），倍速播放功能是RTSP協議獨有的，其他視頻協議都無法支持。
缺點：
就是服務器端的複雜度也比較高，實現起來也比較複雜。Ios端不支持該協議。
 
3、RTMP協議：
   RTMP是Real Time Messaging Protocol（實時消息傳輸協議）的首字母縮寫。RTMP(Real Time Messaging Protocol)實時消息傳送協議是Adobe Systems公司為Flash播放器和服務器之間音頻、視頻和數據傳輸 開發的開放協議。該協議基於TCP，是一個協議族，包括RTMP基本協議及RTMPT/RTMPS/RTMPE等多種變種。RTMP是一種設計用來進行實時數據通信的網絡協議，主要用來在Flash/AIR平台和支持RTMP協議的流媒體/交互服務器之間進行音視頻和數據通信。支持該協議的軟件包括Adobe Media Server/Aoku Media Server/red5/Wowza等。

優點：

支持直播、點播

缺點:
需要專用的服務器。
 
三、協議對比
關於三個RTMP，RTSP，HTTP的對比：
1.RTMP是adobe的，RTSP是 android native支持，http協議。
2.RTMP和HTTP有adaptive streaming的技術，RTSP沒有
3.RTSP實時性是最好的，HTTP實時性比較差。
4.ios不支持rtsp，安卓支持。

四、總結
  三種協議各有優缺點，rtmp協議應用範圍比較窄，一般客戶端需要用flash接收，rtsp一般常用於監控領域和對實時性要求比較高的場合，http的延伸hls用的比較多，一般用在移動終端觀看，一般一個成熟的流媒體服務系統都需要支持這三種協議，甚至更多的協議，比如udp組播，單播，或者p2p協議等。
   Aoku Media Server是可以同時支持這三種流媒體協議的。是國內為數不多的專業流媒體服務系統，提供的免費版可以供用戶進行三種協議的測試對比
```
簡單來說，hls是基於http
但是只有apple支援，所以apple裝置可以直接用
要瀏覽器支援要用[video-dev/hls.js: JavaScript HLS client using Media Source Extension](https://github.com/video-dev/hls.js/)

#### nginx-rtmp-module
~~目前感覺就是跟rtmp與rtmp server串接用~~
[Nginx-rtmp模块实现流媒体play、push、pull功能-Dream come true-51CTO博客](http://blog.51cto.com/lyd0721/1429803)
{% asset_link 1.png 備份圖 %}
```
    Nginx RTMP Module - 架構在Nginx之上, 也算老牌了, 支援RTMP和HLS, 但看code base, 實在也沒啥在更新
    Mona server - 支援RTMP, HTTP(非HLS), Web socket等等
    Red5 Media Server - 支援RTMP, HLS, WebSocket, RTSP, 好像是要錢
    Simpe RTMP Server - 這是由中國的觀止雲這家開源出來的, 講”Simple”其實一點都不Simple, 輕量, 穩定(至少我試直播一晚上都還蠻順利的), 好擴展(支援forward to edge), 可RTMP轉HLS, 因此我最後選擇這個方案
```
其實今天仔細找了一下，SRS跟nginx-rtmp-module只要選其中一種就可以了
參考來源:[[筆記] 中秋連假小實驗 – Le murmure de Julian](http://blog.jln.co/%E7%AD%86%E8%A8%98-%E4%B8%AD%E7%A7%8B%E9%80%A3%E5%81%87%E5%B0%8F%E5%AF%A6%E9%A9%97/)

裡面寫的很詳細

#### 影片/音樂傳流檔案
mp2t  for audio/video
m3u8  for 多媒體列表檔案格式


雖然最近沒有打算做這個相關東西
但是不知道為什麼對hls特別有興趣XD
越記越多，希望哪天有機會用到