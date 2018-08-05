---
title: CSS flex常忘記筆記
date: 2018-06-08 21:20:52
tags: [CSS,flex]
categories: CSS
---

## flex-direction:  column/column-reverse row row-reverse (預設row)

## flex-wrap: wrap nowrap wrap-reverse (default: nowrap)
常忘記有**wrap-reverse**
[Flexbox Froggy - A game for learning CSS flexbox](https://flexboxfroggy.com/)
最後一關會用到他(我卡很久在那邊)

<!--more-->

## flex-flow: flex-direction flex-wrap;

{% asset_img flex_terms.png flex圖示 %}
main axis是主軸線
cross axis是副軸線

**我覺得上面兩個名詞要搞懂會比較好了解**

## justify-content
參考flex-direction主軸線

## align-content(default:stretch)  用在多行  單行可能沒做用
交錯軸上下
## align-items(default:stretch)，有baseline
這兩個常常搞錯
### ※踩雷筆記
當flex裡面元素沒有設定高度，預設為height:auto的話，**stretch會延展高度100%**
相反沒有的話，看內文的寬度，ex:`<div></div>`內文沒有高度和文字，但不限顯示出來，除非打字就會有東西

align-items和align-content公用看起來很像，裡面有詳細介紹
[flex布局中align-items 和align-content的区别 - CSDN博客](https://blog.csdn.net/sinat_27088253/article/details/51532992)
[align-content - CSS：层叠样式表 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/align-content)


## width
flex等於由下面屬性和成
### flex-grow: (default:0)預設不會延展
### flex-shrik:(default:1)預設會縮小
### flex-basis:(default:auto) 依照副軸線對應width或height，長度優先權最高
[flex-item的flex-basis与width属性有什么关系？ - 额，前端 - SegmentFault 思否](https://segmentfault.com/a/1190000005077709)

### 小雷注意
如果沒設定`flex-basis`長度的話(有設定width)，flex-shrik不會把元素縮小....
看來flex-shrik都是依照flex-basis長度縮放為主
可實作看看會很清楚
[JS Bin - JS Bin](http://jsbin.com/beqarabozo/edit?html,css,js,output)

>  如果同时设置flex-basis和width，那么width属性会被覆盖，也就是说flex-basis的优先级比width高。有一点需要注意，如果flex-basis和width其中有一个是auto，那么另外一个非auto的属性优先级会更高。

[深入理解css3中的flex-grow、flex-shrink、flex-basis](http://zhoon.github.io/css3/2014/08/23/flex.html)
有公式


## 心得

其實我覺得flex精華都在flex-grow跟flex-shrik上面，其實我之前就有去了解flex
有玩過Flexbox Froggy遊戲...，但對排版印用不是很了解
flex-shrik預設竟然是1，會自動縮放，但是flex-grow預設是0
也就是說在不預設定`flex`屬性時候
裡面原素預設不會放大，但也不會超過父層寬度
這用在響印式設計真的還不錯

常常看到很多用`flex:1`，會自動擴大
我一直以為每個元素都要設定flex，**但其實不用**

**備註**
flex:1   只會影響flex-grow
可惡...被padding跟margin影響了XD



影片教學可參考
[(1) 卡斯伯 - 20:30 直播 CSS Flex 來試試看 Flex 吧 看看這東西到底是怎麼算的 😆 今天沒有 ZOOM 頻道](https://www.facebook.com/WccCasper/videos/414910278905201/)