---
title: jQuery自訂的ajax Event
date: 2018-04-24 20:39:39
comments: true
tags: [jQuery,ajax,event]
categories: jQuery
---


今天看到[Ajax-Global Ajax Event Handlers .ajaxComplete()、.ajaxError()、.ajaxSend()、.ajaxStart()、.ajaxStop()、.ajaxSuccess() | 阿沙布魯 - 點部落](https://dotblogs.com.tw/brooke/2016/07/10/142933)
平常都不會這樣寫，發現這樣寫可以模組化
今天用少部份時間，來研究出來ajax event來實做出動畫特效
**備註：ajax event都是jQuery自訂的，原JavaScript沒有這個東西**

<!--more-->

拿線上別人寫好的AJAX DEMO小改
[jQuery AJAX request demo - JSFiddle](http://jsfiddle.net/WQXXT/12468/)

```js
$(document).on('ajaxSend',function(){
    $('#overlay').show();
    $('#overlay').width('100%');
    
    $(window).on('beforeunload',function(){
  	    if( confirm('查詢未完成，是否要離開?')){
    	    return true;
        }
        return false;
    });
}).on('ajaxComplete',function(){
    $('#overlay').width('0px');
    $(window).off('beforeunload');
});
  ```

注意：jQuery onload 3.0就不能用了，要使用beforeunload才對
[各个浏览器中对于beforeunload事件和unload事件的对比 | {XFE}](https://sinaad.github.io/xfe/2016/06/29/beforeunlod-vs-unload/)
其實這個東西還可以寫一篇才對.....


原本想說CSS不會花太多時間
結果還是花了一點點....

```css
#overlay{
  width: 0%;
  height: 100%;
  display: none;
  position: fixed;
  top:0px;
  left:0px;
  transition: all .3s;
    overflow: hidden;
}

#overlay_mask{
  width: 100vw;
  height: 100%;
  font-size: 6em;
  background: rgba(0,0,0,0.6);
    display:flex;
    align-items:center;
    justify-content:center;
  /* display:none; */

}

```

`#overlay_mask`那邊花了不少時間解決
原本是用1000px，但覺得應該有更好的
使用`vh`沒有占全版
`vw`就解決了

>vw——代表视窗(Viewport)的宽度为1%，在我们的例子里50vw = 500px。
vh——窗口高度的百分比 50vh = 400px。
vmin——vmin的值是当前vw和vh中较小的值。在我们的例子里因为是横向模式，所以50vim = 400px。
vmax——大尺寸的百分比。50vmax = 500px。
著作权归作者所有。
商业转载请联系作者获得授权,非商业转载请注明出处。
原文: https://www.w3cplus.com/css/simplify-your-stylesheets-with-the-magical-css-viewport-units.html © w3cplus.com
[視區相關單位vw, vh..簡介以及可實際應用場景 - 掃文資訊](https://hk.saowen.com/a/0d16a289be70d5fa8cb0b56df1c3ee78825f3f607e6870e30f769861f566a33b)


花了不少時間，但相信這個對未來實做[[程式][JQuery] 讓AJAX運作的時候，跳出loding的訊息。－Part 1 @ 四處流浪的阿基。I am Vagrant Walker :: 痞客邦 ::](http://expect7.pixnet.net/blog/post/39829979-%5B%E7%A8%8B%E5%BC%8F%5D%5Bjquery%5D-%E8%AE%93ajax%E9%81%8B%E4%BD%9C%E7%9A%84%E6%99%82%E5%80%99%EF%BC%8C%E8%B7%B3%E5%87%BAloding%E7%9A%84%E8%A8%8A)

>    .ajaxComplete() - runs for every request that completes, use this when you want to do something with each request/result. Note that this doesn't replace the success handler, since the parsed data is not one of the arguments (and it runs even when there's an error) - you may want .ajaxSuccess() in some per-request situations instead.
    .ajaxStop() - runs when every batch of requests completes, usually you'd use this in combination with .ajaxStart() for things like showing/hiding a "Loading..." indicator of some sort - or to do something else once a batch of AJAX requests finishes, like a master last step.
[ajax - jquery::ajaxStop() versus jquery::ajaxComplete() - Stack Overflow](https://stackoverflow.com/questions/4419241/jqueryajaxstop-versus-jqueryajaxcomplete)
恩...英文弱弱的...有找一下中文版說明XD
>    .ajaxComplete() ——運行每個請求完成,用這個當你想做一些與每個請求/結果。 注意這並不替換 success 處理程序，因為解析的數據不是一個參數( 即使有錯誤，它也運行) - 你可能需要 .ajaxSuccess() 在某些per-request情況下。
    .ajaxStop() ——運行當完成每批請求,通常你有使用這種結合 .ajaxStart() 例如顯示/隱藏某種"正在載入。"指示器,或者做一些其他一批ajax請求完成後,就像一個主人的最後一步。
[jquery::ajaxStop() 与 jquery::ajaxComplete( )_ajax_帮酷编程问答](http://hant.ask.helplib.com/ajax/post_779385)


相信這個能跟下面UI整合，有空再實做
[waitMe](http://vadimsva.github.io/waitMe/)
[busy-load](https://piccard21.github.io/busy-load/)
[jQuery BlockUI Plugin](http://malsup.com/jquery/block/#page)

因為BlockUI看到是用`$(document).ajaxStop($.unblockUI); `，有去找兩著比較ajaxComplete、ajaxStop
我看到範例是用ajaxComplete，不過照文件說用ajaxStop做顯示/隱藏是最好的樣子



這篇其實有很多雜七雜八的東西，其實要分開寫會比較好...
不過哪貼再踩到雷再補好了
