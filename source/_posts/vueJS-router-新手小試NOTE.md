---
title: vueJS router 新手小試
date: 2018-09-26 21:37:32
tags: [vue]
categories: Javascript
---

最近看了alex直播全家桶，也看了官網router範例
看官網範例可以[vuejs/vue-router: 🚦 The official router for Vue.js.](https://github.com/vuejs/vue-router) git clone回去
```
npm install 
npm run dev
```
有看有懂，但實戰就忘了一堆XD
還是留一下比較
<!--more-->

先小記我寫router問題/心得
有搭配json-server
`json-server db.json --static .`
http://localhost:3000/index.htm
可以進入到網頁

{% asset_link "code.7z" 程式檔案 %}

1. 使用router 帶入param沒法帶vue本生data到component裡面
目前雖然用js閉包做到，但這樣不好看....，目前找不到方法，然到真的用vuex最佳解?

2. router  可以算是全域變數，一直看範例以為要`this.$router`才能用
(目前猜測 `new Vue`出來變數可能用`$xxx`可以抓到)
```
new Vue({
    el,
    router,
    data
})
```

3. 第一次嘗試ES6完整縮寫，看起來清爽多了
`this.directorys.splice(this.directorys.findIndex( el => el.id == id), 1)`

4.  Router傳參數好像不太方便，但說不定有更好的方法(我還沒有嘗試用vuex)
```
// { path: '/edit/:id', component: Edit_c, props: ()=>{ return { data : data.directorys.find( el =>{return el.id == 1 })}  }}
    //帶router帶參數好像只能用props: true
    // { path: '/edit/:id', component: Edit_c, props: ()=>{ return { data : JSON.parse(JSON.stringify(data.directorys.find( el =>{return el.id == 1 })))}  }}
    //router 帶 props 會連動 地雷，可用json parse stringify 來解決
    // 但好像不太能這樣用，一進/edit/xx 會出錯
```

5. ajax,fetch,axios 差別，目前查到的資料fetch是基於promise，axios是`XHR`加Promise，所以很多人都推axios
但我為什麼先用fetch呢?其實是懶得cdnjs找套件XD
```
            fetch('/directorys',{
                method: 'GET'
            }).then((res)=>{
                if (!res.ok) throw new Error(res.statusText)
                return res.json();
            }).then((data)=>{
                this.directorys = data;
            });
```
[Using Fetch - Web APIs | MDN](https://developer.mozilla.org/zh-TW/docs/Web/API/Fetch_API/Using_Fetch)

* [从ajax到fetch、axios - 掘金](https://juejin.im/post/5acde23c5188255cb32e7e76)
* [Jquery ajax, Axios, Fetch区别之我见 - 都选C - SegmentFault 思否](https://segmentfault.com/a/1190000012836882)
* [ajax、axios、fetch之间的详细区别以及优缺点 - CSDN博客](https://blog.csdn.net/twodogya/article/details/80223508)

6.  Array.prototype.find ,Array.prototype.findIndex很實用

7. vuejs的table場景特效可以用`<tbody>`
[transition-group is not working on tables · Issue #3907 · vuejs/vue](https://github.com/vuejs/vue/issues/3907)

8. 到底讓子元件做ajax查詢資料，還是讓父類別做ajax查詢，說真的感覺component做ajax比較好做，但是感覺寫在父層對component自由度會比較大
但相對
目前覺得很死component(只查某一類東西)在component做會比較好
~~不然data binding雙向綁定會搞死人，vue-router又不能使用v-once~~
我現在才想到我是不是因為用`v-model`關係
才導致雙向綁定，也許我該用`v-bind:value`才對  orz



9. input[type=radio]在使用vue data binding，用`JSON.parse(JSON.stringify(xxxx))`轉出物件做bind好像會有問題，後來有想到用computed做，但還沒測試。

10. router-view到底該不該寫event事件呢?我看很多範例都沒有這樣寫，不知道該寫event bus跟vuex哪個比較好，但是vuex還沒學，我想router搞熟在學會比較好

以上有些疑問可能要復仇名單(我有記得的話，再回來改)

總結

這次code寫的很糟糕，感覺有很多地方可以改進
也了解寫template沒有用`vue cli`真的很痛苦
就像寫筆記本沒亮度提示，真的很傷眼睛XD

router 串大量參數感覺真的很不方便，難道真的要靠vuex解決?
有空研究一下還有什麼方法，閉包解法真的不是很好看

感覺`<component is="xxx">`和vue-router比起來
可以做到`頁籤分頁`功能，我想唯一的是vue-router有做到回上一頁功能
說真的這個真的很方便

但網頁上寫commponent真的很痛苦
難道該跳cli了XD

最後突然想到component有一個keep-alive
就在想router有沒有這玩意
[vue-router 之 keep-alive - 简书](https://www.jianshu.com/p/0b0222954483)

最後又想到event問題，官方文件沒有提到
但我實作一下，好像可以用
