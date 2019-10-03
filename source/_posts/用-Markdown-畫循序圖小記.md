---
title: 用 Markdown 畫循序圖小記
date: 2019-10-01 23:09:57
tags: [markdown]
categories: Markdown
---

小記我常忘 markdown 循序圖

<!--more-->

循序圖

```sequence
战士->领导: 首长好
Note right of 领导: 首长复杂的内心活动
领导-->战士: 同志们好
战士->>领导: 为人民服务
```

```js
對象箭頭標識符：
    ->: 表示實線實箭頭 (同步)
    -->: 表示虛線實箭頭 兩個--回復
    ->>: 表示實線虛箭頭(非同步)
    -->>：表示虛線虛箭頭
```

[【UML 建模】UML入门 之 交互图 -- 时序图 协作图详解 - 让 学习 成为一种 习惯 ( 韩曙亮 の 技术博客 ) - CSDN博客](https://blog.csdn.net/shulianghan/article/details/17927131)
[Markdown 時序圖/序列圖 - 開發者目錄](https://www.rdtoc.com/tutorial/markdown-js-sequence-tutorial.html)
[UML 图中的消息](https://www.ibm.com/support/knowledgecenter/zh/SS4JE2_7.5.5/com.ibm.xtools.sequence.doc/topics/cmsg_v.html)

`-->` 程式返回值使用，如上面範例
