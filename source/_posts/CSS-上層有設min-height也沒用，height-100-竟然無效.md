---
title: '[CSS]上層有設min-height也沒用，height:100%竟然無效??'
date: 2018-06-24 00:55:16
tags: [css,min-height,height]
categories: CSS
---

父flex
子height:100%;
竟然不會延展
不過flex
好像不用設定height:100%就會延展(default:stretch)

<!--more-->

同樣div父層設定min-height:100px;
div子層height:100%;
不會變大，可在子層設定min-height:inherit
[CSS中height:100%和height:inherit的异同 « 张鑫旭-鑫空间-鑫生活](http://www.zhangxinxu.com/wordpress/2015/02/different-height-100-height-inherit/)
`在她裡面`，我瞬間背起來單字了
這個背法還真好背，哈哈


### 解法
不過還是有找到解法，設定父position:relative和子position:absolute，父不用設定height就可以讓子height:100%，是不是很神奇?!!

2018-07-08
今天做week5 前端修練時光屋發現我有一個高度怪怪的(是用position:absolute) 有加到padding-top:20
後來查了一下
left + margin-left + border-left-width + padding-left + width + padding-right + border-right-width + margin-right + right = 包含块宽度

預設position:static的高度是算 content-box 沒有padding跟margin
所以可能用absolute跟relative要注意一下他常寬的算法，可能會踩到雷XD

>Your CSS means that the child element's height is 100% of the specified height of the parent element. If you do not specify a height for the parent, then the 100% doesn't mean anything. Hence it doesn't work.
>What you want can be achieved by using position:relative on the parent and position:absolute on the child:
>http://jsfiddle.net/57EZn/25/
>It's not a beautiful solution but it does what you are after.

[html - CSS 100% height of container with min-height - Stack Overflow](https://stackoverflow.com/questions/6995581/css-100-height-of-container-with-min-height)

[关于height：100%无效的解决办法与细节 - CSDN博客](https://blog.csdn.net/SuperCoooooder/article/details/52469520)
[从height:100%不支持聊聊CSS中的“死循环” « 张鑫旭-鑫空间-鑫生活](http://www.zhangxinxu.com/wordpress/2016/09/talking-about-css-infinite-endless-loop/)
[CSS中height:100%和height:inherit的异同 « 张鑫旭-鑫空间-鑫生活](http://www.zhangxinxu.com/wordpress/2015/02/different-height-100-height-inherit/)

[JavaScript中的Date对象，以及UTC、GMT、时区的关系 | xtutu](http://xtutu.me/the-date-object-in-js/)
