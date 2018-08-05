---
title: 'flex margin-right:auto 右邊元素靠右(參考bootstrap4 navbar)'
date: 2018-07-02 23:13:12
tags: [flex,CSS,margin]
categories: CSS
---

今天看到官網Bootstrap 4的Nabars，好奇他的Navbar怎麼排版的
發現竟然是使用flex去編排，但是搜尋位置竟然可以靠右
中間空白是怎麼用的??查了一下，發現他有使用margin
究竟flex和margin碰在一起會產生甚麼化學反應呢?
讓我們紀錄吧!!!

<!--more-->

在講flex之前，我就有紀錄過margin搭配position可以置中，但其他是有公式的
>'margin-left' + 'border-left-width' + 'padding-left' + 'width' + 'padding-right' +'border-right-width' + 'margin-right' = width of containing block
[margin-left: auto;为什么可以使的元素靠右 - SharkU的回答 - SegmentFault 思否](https://segmentfault.com/q/1010000008431088/a-1020000008431730)

>margin-right 不设置的话默认是0，width 定宽之后，margin-left取值为 auto ，自动占据了剩余的全部宽度，具体见 《css权威指南》 P170
[studyFiles/CSS权威指南（第三版）(jb51.net).pdf at master · threerocks/studyFiles](https://github.com/threerocks/studyFiles/blob/master/%E5%89%8D%E7%AB%AF/CSS%E6%9D%83%E5%A8%81%E6%8C%87%E5%8D%97%EF%BC%88%E7%AC%AC%E4%B8%89%E7%89%88%EF%BC%89(jb51.net).pdf)


bootstrap 4 是用 左邊最後一個元素加上`margin-right:auto` 右邊會靠右
當然反過來也可以在左邊search 加上`margin-left:auto`

在簡書看到我想知道的東西(備份:https://screenshots.firefox.com/0gK3HJq4OS6WkyCk/www.jianshu.com)
>Flex布局算法中对auto margins的对齐原理有如下解释，原文是英文，我尽量准确翻译：
    在主轴（main axis）方向
        如剩余空间为正数，则剩余空间会被平均分配在拥有主轴方向auto margin(s)的flex元素之间。
        如剩余空间为负数，则将主轴方向的auto margin(s)设定为‘0’。

>看过这些例图，auto margins的使用方法应该不会忘了吧。最后再来提个醒儿：
   * auto margins仅针对当前的flex item，对其子元素无效。
   * 在计算flex-bases与flexible lengths （main size与cross size）时，auto margins取值为0。
   * auto margins优先于justify-content与align-self。由于相应方向上的剩余空间已分配给auto margins，justify-content与align-self将无效。
   * auto margins对溢出的（overflowing）flex item无效。

作者：蜂蜜柚子茶
链接：https://www.jianshu.com/p/beeb93a76830
來源：简书

沒想到還能這樣用，筆記筆記...
