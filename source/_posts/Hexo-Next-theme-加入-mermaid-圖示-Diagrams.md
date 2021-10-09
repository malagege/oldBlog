---
title: Hexo Next theme 加入 mermaid 圖示(Diagrams)
date: 2021-10-09 23:31:25
tags: [hexo,mermaid]
categories: Hexo
---


原本參考[Hexo中插入mermaid diagrams | Yu's Notes](https://wangxiaoyu-go.github.io/2018/11/23/hexo-filter-mermaid-diagrams/)，不過做到一半還是失敗。最後還是解決了，順便紀錄一下。

<!--more-->

## 使用 Next 裡面設定好的 mermaid

theme/next-reloaded/_config.yaml
```
mermaid:
  enable: true
  # Available themes: default | dark | forest | neutral
  theme: forest

```

下面可以不用看了!!
下面可以不用看了!!
下面可以不用看了!!

## 失敗安裝紀錄

先commit 現有 hexo GIT專案，防止壞掉可以還原。

```bash=
npm install hexo-filter-mermaid-diagrams
```

先前使用[Hexo NexT 主題遇到連結 %20 空白問題，新版設定 Google Adsense 方法 | 程式狂想筆記](https://malagege.github.io/blog/2021/06/14/Hexo-NexT-%E4%B8%BB%E9%A1%8C%E9%81%87%E5%88%B0%E9%80%A3%E7%B5%90-20-%E7%A9%BA%E7%99%BD%E5%95%8F%E9%A1%8C%EF%BC%8C%E6%96%B0%E7%89%88%E8%A8%AD%E5%AE%9A-Google-Adsense-%E6%96%B9%E6%B3%95/)，所以在加入 HTML 程式做調整。我先前使用 njk，這樣我程式碼不用改在 theme 裡面。

官方都有提供對應程式碼調整，參考如下圖。
![](https://i.imgur.com/FwQAXEY.png)



theme/next/_config.yaml
下面 footer 請打開
```yaml=
custom_file_path:
  #head: source/_data/head.swig
  header: source/_data/header.njk
  sidebar: source/_data/sidebar.njk
  postMeta: source/_data/post-meta.njk
  postBodyEnd: source/_data/post-body-end.njk
  footer: source/_data/footer.njk
  #bodyEnd: source/_data/body-end.swig
  #variable: source/_data/variables.styl
  #mixin: source/_data/mixins.styl
  #style: source/_data/styles.styl

```

失敗的話，我也是這樣QQ
不過後來發現原本 Next 就有支援了。