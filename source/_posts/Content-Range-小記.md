---
title: Content-Range 小記
date: 2019-06-27 23:40:56
tags: [range]
categories: HTML
---

最近做音樂串流， IOS 發生了不能播放音樂問題
找了很久，看了一下封包
結果發現一見很怪的事情

<!--more-->

IOS 發送 `Range: bytes=0-1`
但是 Server 正常回傳
但 IOS 會無限發送 `Range: bytes=0-1`

- [记一则流媒体传输上的坑 | Wildog](https://wil.dog/2016/09/07/streaming-with-flask/)
- [iOS h5 点播播放 mp4 视频遇到的坑,ios 的 h5 不能播放视频等 - 简书](https://www.jianshu.com/p/d0dbd3196a8e)
- [使用 PHP 读取视频流，IOS 系统无法观看的问题解决方法 – Jeffrey Wang 的个人博客](https://blog.wj2015.com/2018/03/10/%E4%BD%BF%E7%94%A8php%E8%AF%BB%E5%8F%96%E8%A7%86%E9%A2%91%E6%B5%81%EF%BC%8Cios%E7%B3%BB%E7%BB%9F%E6%97%A0%E6%B3%95%E8%A7%82%E7%9C%8B%E7%9A%84%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3%E6%96%B9%E6%B3%95/)

- [Swift HTTPURLResponse 的一个坑 - 简书](https://www.jianshu.com/p/a9360d0ff316)
- [HTTP/2 约束 Header 大小写 - phantomVK Blog](https://phantomvk.github.io/2018/12/13/HTTP2_Header_lower_case/)

簡單小記

request header:
Range: bytes=0-x

response header:
Content-Length: 0-x/n

[聊一聊 HTTP 的 Range, Content-Range | HappyCoding:)](https://dabing1022.github.io/2016/12/24/%E8%81%8A%E4%B8%80%E8%81%8AHTTP%E7%9A%84Range,%20Content-Range/)

## 延升想法

最近同事串流 CDN 服務
再想用這種 `Range`用 https 去做 Range 感覺沒有到很方便
現在很多用 hls(m3u8)方式，可能就不會遇到這種問題 orz

剛打完就剛好看到有人講到[video 标签在 iPhone 手机上不能播放？万能的知乎大神请指引我，实在没办法了 - 知乎](https://www.zhihu.com/question/41818719)
