---
title: hexo在blog貼圖
comments: true
date: 2018-04-08 21:19:17
tags: [hexo,img]
categories: 寫程式一些事
---



hexo設定貼圖方法
在
```yml config.yml
post_asset_folder: true
```

```
{% asset_path slug %}
{% asset_img slug [title] %}
{% asset_link slug [title] %}
```

參考來源
[資產資料夾 | Hexo](https://hexo.io/zh-tw/docs/asset-folders.html)
