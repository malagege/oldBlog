---
title: Vue Component 沒有 Render 出來
date: 2021-08-22 15:45:42
tags: [vue]
categories: Vue
---

最近`setup`寫太順，身為新手的我還是先用一般寫法寫。結果遇到怪事， components 竟然叫不出來，結果我用回`setup`又就可以用了...

![](https://i.imgur.com/t5xKozp.png)


<!--more-->

結果想到 Vue component 要註冊才能用元件，結果補上就能用了...

```javascript
export default{
  components:{
    Header,MyAnimeList
  }
}
```