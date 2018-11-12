---
title: vue-chat-scroll和MutationObserver小記
date: 2018-10-24 22:10:09
tags: [vue]
categories: [JavaScript]
---

最近突然想起以前看到有一個加項目，卷軸超過其範圍會自動往下
印象中...是個DOM改變會自動偵測
所以我以為是listiner
但不是....

<!--more-->


[theomessin/vue-chat-scroll: Automatic scroll-to-bottom directive for vue.](https://github.com/theomessin/vue-chat-scroll)
[Vue 插件開發 - 聊天視窗滾動與自動加載，關於MutationObserver/Custom Directive « YJ Blog](http://sj82516-blog.logdown.com/posts/3095129)

簡單，使用就不說了



原理是用MutationObserver
但是不管DOM修改1000次，做rendering才會觸發
小玩官網範例[vue-chat-scroll](https://theomessin.github.io/vue-chat-scroll/)
可以看以下我小玩Promise

官網是for一次回圈非同步處理，所以才會觸發很多次MutationObserver

```html
<button @click="pushMsg">add item</button>
    <h1>message</h1>
    <div class="messages" v-chat-scroll="{always: false}">
      <div class="message" v-for="n in messages" :key="n">{{ n }}</div>
    </div>
```

```js
...
pushMsg(){
        // for (let i = 0 ; i < 30 ; i++)
        // fetch('https://baconipsum.com/api/?type=meat-and-filler&sentences=1&start-with-lorem=1').then(function (response) {
        //   return response.json()
        // }).then(data => this.messages.push(data[0]))

        var temp = []
        for (let i = 0 ; i < 30 ; i++)
        temp.push(fetch('https://baconipsum.com/api/?type=meat-and-filler&sentences=1&start-with-lorem=1').then(function (response) {
          return response.json()
        }))

        Promise.all(temp).then(data => data.forEach(e => this.messages.push(e)))
        

        // for (let i = 0 ; i < 30 ; i++)
        //   this.messages.push("data[0]")

    }

```

## MutationObserver 

簡單紀錄一下
MutationObserver 如上面所說`不管DOM修改1000次，做rendering才會觸發`
所以它也不算一個listener原因

除了範例對節點，它還可以對`屬性`、`內容`、`子層html`
詳細看文件[MutationObserver - Web APIs | MDN](https://developer.mozilla.org/zh-TW/docs/Web/API/MutationObserver)

由於很少網站資訊介紹MutationObserver
[Mutation Observer API -- JavaScript 标准参考教程（alpha）](http://javascript.ruanyifeng.com/dom/mutationobserver.html)
這個網站算介紹滿清楚的
{% asset_link web_bk.png 備份圖 %}

```
var article = document.querySelector('article');

var  options = {
  'childList': true,
  'attributes':true
} ;

observer.observe(article, options);

上面代码中，observe方法接受两个参数，第一个是所要观察的DOM元素是article，第二个是所要观察的变动类型（子节点变动和属性变动）。

观察器所能观察的 DOM 变动类型（即上面代码的options对象），有以下几种。

    childList：子节点的变动（指新增，删除或者更改）。
    attributes：属性的变动。
    characterData：节点内容或节点文本的变动。

想要观察哪一种变动类型，就在option对象中指定它的值为true。需要注意的是，必须同时指定childList、attributes和characterData中的一种或多种，若未均指定将报错。

除了变动类型，options对象还可以设定以下属性：

    subtree：布尔值，表示是否将该观察器应用于该节点的所有后代节点。
    attributeOldValue：布尔值，表示观察attributes变动时，是否需要记录变动前的属性值。
    characterDataOldValue：布尔值，表示观察characterData变动时，是否需要记录变动前的值。
    attributeFilter：数组，表示需要观察的特定属性（比如['class','src']）。

// 开始监听文档根节点（即<html>标签）的变动
mutationObserver.observe(document.documentElement, {
  attributes: true,
  characterData: true,
  childList: true,
  subtree: true,
  attributeOldValue: true,
  characterDataOldValue: true
});

```


雖然除了聊天室感覺很需要這個功能
但還想不到什麼情境可以用到....
要真的用其實直接用[theomessin/vue-chat-scroll: Automatic scroll-to-bottom directive for vue.](https://github.com/theomessin/vue-chat-scroll)
就可以了XD
