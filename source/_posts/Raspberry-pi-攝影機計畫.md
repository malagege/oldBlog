---
title: Raspberry PI 攝影機計畫之新手簡單架設 HLS 和 RTSP Server
date: 2021-08-22 15:31:57
tags: [pi,camera,raspberry]
categories: Linux
---

最近打算 Raspberry Camera 或 小米攝影機做監控發送訊息通知，所以先行研究 RSTP，想說之後串 OpenCV 可以先測試結果，不過沒想到 RTSP Server 就搞了那麼久...。

彩蛋就先放這: 我Raspberry PI Camera 找的方案

- [MieszkoMakuch/pi-security-camera: University project: Smart security camera on raspberry pi with email notifications and web interface](https://github.com/MieszkoMakuch/pi-security-camera)

- [(1) How to Make a Smart Security Camera with a Raspberry Pi Zero - YouTube](https://www.youtube.com/watch?v=Y2QFu-tTvTI)

- [chollinger93/scarecrow: A Raspberry Pi powered, distributed (edge) computing camera setups that runs a Tensorflow object detection model to determine whether a person is on the camera. A plugin model allows actions based on the detection, such as playing audio, turning on lights, or triggering an Arduino.](https://github.com/chollinger93/scarecrow)

- [(1) Raspberry Pi Camera VS NOIR Camera - YouTube](https://www.youtube.com/watch?v=lP-QQGxm2Y0)

[shbatm/MMM-RTSPStream: MagicMirror² module for streaming an RTSP video stream from a security camera to your MagicMirror.](https://github.com/shbatm/MMM-RTSPStream)

結果我後來放棄這個方案，使用打算使用 Motioneye。

<!--more-->

## 專有名詞

可以看[Streaming 通訊協定 RTP RTCP RTSP RTMP HLS 介紹 | Jason note](https://jasonblog.github.io/note/media_player/streaming_tong_xun_xie_ding_rtp_rtcp_rtsp_rtmp_hls.html)，之前也因為好奇有小記，但沒有實作。[hls影音串流小記 | 程式狂想筆記](https://malagege.github.io/blog/2018/11/13/hls%E5%BD%B1%E9%9F%B3%E4%B8%B2%E6%B5%81%E5%B0%8F%E8%A8%98/)
[(1) 直播 小米監視器的畫面 比較不同播放器 2種協定 RTMP HLS 的延遲時間比較 - YouTube](https://www.youtube.com/watch?v=w8OtVRTw9io)
## rtsp-simple-server
架設成功，簡單小記一下。

### Github

https://github.com/Ullaakut/RTSPAllTheThings
https://github.com/aler9/rtsp-simple-server

### 安裝過程

docker 架設 Server UDP會有問題。所以使用 Docker 要設定 TCP 選項。
參考這個:[video - Streaming RTSP with ffmpeg? - Super User](https://superuser.com/questions/1420328/streaming-rtsp-with-ffmpeg)

1. rtsp-simple-server.yml 設定 tcp。
```
protocols: [tcp]
```
2. 啟動 Container，這邊使用 host 模式，所以對印 IP 是原主機 IP。
```
docker run --rm -it -v $PWD/rtsp-simple-server.yml:/rtsp-simple-server.yml --network=host aler9/rtsp-simple-server
```

3. live/streamname 可以自已改路徑

```
ffmpeg -re -stream_loop -1 -i test.mp4 -f rtsp -rtsp_transport tcp rtsp://localhost:8554/live/streamname
```

4. hfs 看直播

http://192.168.1.111:8888/live/afd/

簡單就完成了

## OBS 設定

![](https://i.imgur.com/qpevos4.png)

簡單新增一個影片 DEMO，然後設定串流。

![](https://i.imgur.com/SZ7d8cb.png)

這邊伺服器就是 rtmp-server IP(注意不是 RSTP)，這邊讓我卡了很久，不過是我沒看清楚教學設定。orz

串流金鑰其實就是`路徑`。

### 相關路徑

這邊路徑是設定`mystream`，相對 hls 設定 `http://192.168.1.111:8888/mystream/`就能看了。

#### hls

hls: http://192.168.1.111:8888/mystream/
m3u8 路徑: http://192.168.1.111:8888/mystream/stream.m3u8

#### rtmp 

rtmp 路徑: rtmp://192.168.1.111/mystream

### 失敗記錄(請跳過)

https://github.com/aler9/rtsp-simple-server/issues/39

![](https://i.imgur.com/MFFsOd9.png)

http://hxz.ink/2016/04/06/broken-pipe/
http://dacongyun.com/archives/231146.html
https://blog.csdn.net/chy555chy/article/details/
[我想问下 rtsp 转 rtmp 怎么解决多个监控摄像头同时转 cpu 过高的问题 - V2EX](https://www.v2ex.com/t/467853?hmsr=toutiao.io&utm_campaign=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io)

最後用這個解決[video - Streaming RTSP with ffmpeg? - Super User](https://superuser.com/questions/1420328/streaming-rtsp-with-ffmpeg)
不過有聲音沒畫面....

## 影片直播

```bash=
ffmpeg -re -stream_loop -1 -i test.mp4 -vcodec libx264 -acodec aac -f flv rtmp://192.168.1.111/live/afd       
```

## nginx-rtmp

[Nginx+RTMP/HLS docker image](https://blog.robodock.net/docker-nginx-rtmp-hls/) [備份圖](https://i.imgur.com/XEhjRJy.jpg)

```
docker run --rm -p 1935:1935 -p 8888:8080 jasonrivers/nginx-rtmp    
```


~~失敗。這邊教學照做不知道為什麼不行....~~我眼殘，OBS設定串流用成 RTSP，記得選 RTSP

## ffmpeg 

[rtsp 转 ts文件 .m3u8的方式_CSDN_Wolf_HQ的博客-CSDN博客](https://blog.csdn.net/CSDN_Wolf_HQ/article/details/93894941?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_v2~rank_aggregation-12-93894941.pc_agg_rank_aggregation&utm_term=rtsp%E8%BD%ACm3u8&spm=1000.2123.3001.4430)



[ffmpeg 抓取rtmp流_Dovake的博客-CSDN博客](https://blog.csdn.net/qq_30340349/article/details/90240112)

### linux /window 抓攝影機



windows :
可以使用 choco 安裝，或者去官方抓檔案下載，並且設定環境變數path

列裝置
```
ffmpeg -list_devices true -f dshow -i dummy
```
![](https://i.imgur.com/Vrncqg8.png)
這邊可以看到，我的攝影機名稱為「HP TrueVision HD Camera」
編碼為H.264，儲存為本地檔案
```
ffmpeg -f dshow -i video="Integrated Camera" -vcodec libx264 mycamera.mkv
```
播放
```
ffplay -f dshow -i video="Integrated Camera"
```
linux :

看格式，要使用 root 
```
ffmpeg -f v4l2 -list_formats all -i /dev/video0
```
錄影 
```
ffmpeg -f v4l2 -framerate 25 -video_size 640x480 -i /dev/video0 output.mkv
```

轉 rtmp ，不知道網的相機太爛?hls 網頁看不到畫面(一直轉)， Potplayer 可以看到內容。

```bash=
ffmpeg -f v4l2 -framerate 25 -video_size 640x480 -i /dev/video0 -vcodec libx264 -acodec aac -f flv rtmp://192.168.1.111/live/afd 
```


window 無法用不知道為什麼，還遇到各種奇奇怪怪狀況，所以放棄測試。

```
ffmpeg  -y -rtbufsize 100M -f dshow -i video="HP TrueVision HD Camera" -vcodec libx264 -acodec aac -f flv rtmp://192.168.1.111/live/sss 
```
