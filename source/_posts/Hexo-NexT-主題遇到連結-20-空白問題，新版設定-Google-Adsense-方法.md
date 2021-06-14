---
title: Hexo NexT 主題遇到連結 %20 空白問題，新版設定 Google Adsense 方法
date: 2021-06-14 14:31:04
tags: [hexo,next]
categories: Hexo
---

最近升級到 node 14，遇到 Hexo 各種問題。雖然有升到 Hexo 4.2 來解決問題，不過還是遇到了一些問題，想說不影響畫面輸出就沒什麼關係，但讓我的網頁 404 就必需要解決了。

<!-- more -->

## 問題整理

1. 上下頁數顯示不正常
![](https://i.imgur.com/dRsW6UD.png)
![](https://i.imgur.com/mjq89AZ.png)

2. 執行跑出以下錯誤
![](https://i.imgur.com/qSBoZw7.png)

## 解決問題 1

我有找到顯示 Hexo 錯誤方法。

[解決 Hexo 在使用 Node.js 14 時的 Accessing non-existent property 'xxx' of module exports inside circular dependency 問題 - 好一則博](https://www.haoyizebo.com/posts/710984d0/)

```bash=
npx cross-env NODE_OPTIONS="--trace-warnings" hexo s
```

恩...，看不懂是什麼錯誤~~跳過~~
設定 package.json 還是無法排除...
所以先放棄

後來爬了一下文章，是 NexT 舊版本問題造成的。升級上去就能解決。


[extra space of menu in default _config.yml cause category unable to jump in menu because of %20 · Issue #1315 · theme-next/hexo-theme-next](https://github.com/theme-next/hexo-theme-next/issues/1315)

就感快升級解決。可以參考[hexo-theme-next/UPDATE-FROM-5.1.X.md at master · theme-next/hexo-theme-next](https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/UPDATE-FROM-5.1.X.md)
簡單來說，舊版的 Next Theme 不用刪掉，只要clone 新得資料夾，就選定新的資料夾主題，最後用 VScode 比較`_config.yml`檔案就能做簡單移轉。

~~太久沒用了，都忘了當出怎麼搞這些畫面~~
順便回想，當出怎麼設定東西

這邊又遇到奇怪的東西，官方新版首頁也掛掉了...

## 問題整理 2

1. 出現不正確名稱~~WTF~~
![](https://i.imgur.com/cQOr3A5.png)

剛好網路上看到說找不到語系，預設會是德文
解決方式：在hexo/_config.yml 的 language 設定`zh-
TW` (大小寫要一致)
![](https://i.imgur.com/e5THho8.png)

```
hexo clean
hexo s
```

就能看到畫面正常了

## 番外篇:Google Ads

發現新版，NEXT 主題做到無侵入式修改主題
我覺得還滿方便，可以來試試看。


後來看報這篇[版更HEXO NEXT 6 → 8，以非侵入式修改整合AdSense、like coin、修正gitlab-ci文章更新時間錯誤的問題 | 只是個打字的](https://blog.typeart.cc/upgrade-nexo-next-6-to-8-and-integrate-adsense-like-button/)介紹[hsuanyi-chou/hexo-next-theme-integration: Hexo NexT主題 V8 懶人包 / Hexo NexT theme integrate google adSense & like coin button without intrusive modification template.](https://github.com/hsuanyi-chou/hexo-next-theme-integration)，這個可以快速使用無侵入方式設定Google Adsense，但我已經套用原官方主題，所以把這個`_data`抓下來使用，再把下面設定上去就可以咯

設定參考[版更HEXO NEXT 6 → 8，以非侵入式修改整合AdSense、like coin、修正gitlab-ci文章更新時間錯誤的問題 | 只是個打字的](https://blog.typeart.cc/upgrade-nexo-next-6-to-8-and-integrate-adsense-like-button/)，建議去裡面看更完整內容

next/_config.yml
```yaml=
# ---------------------------------------------------------------
# Custom Settings
# ---------------------------------------------------------------
# Google AdSense
google_adsense:
  enable: false
  google_ad_client_id: # <client id>
  enable_page_level_ads: false
  ## sidebar_enable
  sidebar_enable: false
  sidebar_data_ad_slot: # <slot id>
  ## post header
  postheader_enable: false
  postheader_data_ad_slot: # <slot id>
  ## post footer
  postfooter_enable: false
  postfooter_data_ad_slot: # <slot id>
```

這個在設定啟用

```yaml=
custom_file_path:
  #head: source/_data/head.njk
  header: source/_data/header.njk
  sidebar: source/_data/sidebar.njk
  postMeta: source/_data/post-meta.njk
  postBodyEnd: source/_data/post-body-end.njk
  #footer: source/_data/footer.njk
  #bodyEnd: source/_data/body-end.njk
  #variable: source/_data/variables.styl
  #mixin: source/_data/mixins.styl
  style: source/_data/styles.styl
```

切記要把swig換成njk。


PS: 舊版設定
[hexo NexT主題增加AdSense | 只是個打字的](https://blog.typeart.cc/hexo%20NexT%E4%B8%BB%E9%A1%8C%E5%A2%9E%E5%8A%A0AdSense/)
[NexT 使用文檔](https://theme-next.iissnan.com/)


還有不少錯誤，但至少可以跑...
先讓子彈飛一會兒
也許砍掉重練會比較快XD