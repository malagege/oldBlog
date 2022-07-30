---
title: Vue-myAnimeList 產生我的動畫清單
date: 2022-07-31 00:21:49
tags: [vue]
categories: Vue
---

完成品:[我的新番清單](https://malagege.github.io/vue-myanimelist/)

因為先前用 jQuery 寫出來版本不能跟朋友討論最近新番。連結在這:[201507新番表](http://malagege.github.io/generator-myanimelist/)

我想實作可以類似可以討論作品，就做出這個玩意。
小記這版特點
1. 使用 Github Action 更新清單
2. 右鍵可以做動畫排名(中圖想到功能)
3. 這邊看到功能有兩大項，一個是本季月番，另一個是總月番顯示內容
這個是我朋友開的 Issue，因為我朋友說不能跨季排名，這個非常不方便。這邊我原本煩惱要怎麼做比較好，原本想說多那個功能需要花七天時間，後來再想有沒有更好的做法，後來想到可以用模組化，大大減少我程式撰寫，花了三天時間完成(下班時間，一天大概2~3小時)。源碼:[vue-myanimelist/MyAnimeList.vue at master · malagege/vue-myanimelist](https://github.com/malagege/vue-myanimelist/blob/master/src/components/MyAnimeList.vue)
4. 可以用網址分享
5. 可存檔功能，這也是我朋友開的 issue 。因為他覺得要紀錄網址太麻煩了。我原本也不太能確定有時間時作，因為要用這個特別做這個功能要額外花時間太麻煩了(身為上班族下班時間可是非常寶貴)，我在想有沒有更簡單的作法。後來想到共用方法可以寫元件，想說值得嘗試。源碼[vue-myanimelist/SettingManager.vue at master · malagege/vue-myanimelist](https://github.com/malagege/vue-myanimelist/blob/master/src/components/SettingManager.vue)

上面`3`和`5`真的讓我寫這個非常有成就，我覺得如何寫出共用元件可以讓下一個專案使用，減少我們後續製作程式的成本這個非常重要。
最後我其實是後端工程師，久久寫 Vue，不知道下次寫我還記得 Vue 有多少...

可改進:

1. url 那一段載入很亂，因為有 bug 之下，寫到有點暈，所以就暴力寫出來了
2. SettingManager 讀取有 bug ，因為要點兩下
3. 當下同網頁貼別人網址無法載入清單



<!--more-->

## 參考項目/連結:
[做了一個自動補新番工具](https://blog.bgpsekai.club/newban-auto-downloader-intro/)

[dmhy_newban_downloader/NewBanCrawler.js at 51662aba9e0caf3c7b76fdc24484db36c59fc76a · a9650615/dmhy_newban_downloader](https://github.com/a9650615/dmhy_newban_downloader/blob/51662aba9e0caf3c7b76fdc24484db36c59fc76a/src/lib/NewBanCrawler.js#L32)


[ACGNTaiwan/Anime-List: 展示新番的清單](https://github.com/ACGNTaiwan/Anime-List)


[YuC's AnimeList](https://yuc.wiki/)


[全网最全的动漫新番放送表情报信息网站推荐 - Go 2 Think](https://go2think.com/bangumi-animelist/)

```javascript=
(function(){
let res = Array(document.querySelectorAll('.title_cn').length).fill({
    "name": "",
    "date": "",
    "time": "",
    "carrier": "",
    "season": 1,
    "originalName": "",
    "img": "",
    "official": "",
    "description": "",
    "staff":"",
})
res = JSON.parse(JSON.stringify(res))
document.querySelectorAll('.title_cn').forEach((el, i) => {
    res[i].name = el.textContent.replace(/第(.+)期/, '')
    res[i].season = parseInt(el.textContent.match(/第(.+)期/) ? el.textContent.match(/第(.+)期/)[1] : 1)
});
document.querySelectorAll('.title_jp').forEach((el, i) => {
    res[i].originalName = el.textContent.replace(/第(.+)期/, '')
});
document.querySelectorAll('.link_a').forEach((el, i) => {
    let qsA = el.querySelector('a')
    if (qsA) res[i].official = qsA.href
});
document.querySelectorAll(`td[class^="type"]:nth-child(even)`).forEach((el, i) => {
    res[i].carrier = { a: "Original", b: "Comic", c: "Novel", d: "Game" }[el.className.replace(`type_`, '')] || "Original"
});
document.querySelectorAll('img[width="180px"]').forEach((el, i) => {
    if (el) res[i].img = el.src
});
document.querySelectorAll('.staff').forEach((el, i) => {
    if (el) res[i].staff = el.textContent
});
})();
```



```javascript=
urls = $('.links-of-blogroll-item a').get().map(el => el.href)
listname = $('.links-of-blogroll-item a').get()
        .map(el=> el.innerHTML)
        .filter(str => str.indexOf('新番') > 0)
        .map(str=>{
            const regex = /(\d+年\d+月新番)/gm;
            m = regex.exec(str)
            return m[0]
        })

```