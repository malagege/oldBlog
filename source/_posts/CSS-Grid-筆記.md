---
title: CSS Grid 筆記
date: 2021-10-09 23:21:15
tags: [css,grid]
categories: CSS
---

# CSS Grid 筆記(writed)

推薦新法先看[[CSS] 關於 Grid Layout 的使用姿勢](https://blog.hinablue.me/css-grid-layout/)，了解觀念在看語法內容比較不會誤會。


<!--more-->

## grid-auto-flow

### 先前使用 grid(錯誤觀念)

```css=
display: grid
```

就會變縱向，但是因為沒有設定 `grid-template`,`grid-auto-columns`所以只會有一列，所以設定上還是了解一下比較清楚。

### grid-auto-flow (排序方向)

[CSS grid-auto-flow深入理解 « 张鑫旭-鑫空间-鑫生活](https://www.zhangxinxu.com/wordpress/2020/01/css-grid-auto-flow/)

grid-auto-flow : row


[grid-auto-flow - CSS: Cascading Style Sheets | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-auto-flow)

## fr 是什麼?

> 首先大家一定要搞清楚一点，1fr 1fr并不等于50% 50%，虽然很多时候表现是一样的，但只是grid格子正好和设定数量一致的时候。1fr 1fr更准确的含义是，可用剩余空间分别50% 50%。

我這邊看到 fr 塞超過 grid 所容量，結果 fr 都那一塊都沒辦法正常顯示，原來是`剩餘空間`關係造成的。

### fr 跟 auto 呢


### 使用 demo 測試 fr 跟 auto

例用 [CSS grid-auto-flow深入理解 « 张鑫旭-鑫空间-鑫生活](https://www.zhangxinxu.com/wordpress/2020/01/css-grid-auto-flow/)
 裡面「grid佈局實現圖像A|B+C佈局demo」範例來看 grid 的fr 和 auto 差異。

前置作業，使用CSS加圖序號清單。

![](https://i.imgur.com/h8aC0bz.png)


```css=
zxx-grid zxx-item::before{
  content: "0" counter(section);
  font-weight: bold;
  font-size: 3rem;
  line-height: 1;
  position: absolute;
  right: 0px;
  bottom: 0px;
}

zxx-grid zxx-item{
  position: relative;
  counter-increment: section;
}
```

使用開發者工具，在後面加入多個圖。

![](https://i.imgur.com/IGRHZny.png)

#### grid-template: 1fr 1fr / 1fr 1fr 1fr

可以看到因為後面加進來的大小都是原圖大小(grid-auto-row,grid-auto-column)，然後所暫空間都用滿了，所以圖片都看不到。

![](https://i.imgur.com/Ud3AjGi.jpg)


#### auto auto / auto auto auto

`auto`會以最小圖來成現。 TODO: 確認

![](https://i.imgur.com/8yLpqNK.jpg)


這邊小記一下，寬度會受影響，但高度確不會，因為高度沒有100%的問題。

![](https://i.imgur.com/fJLpzen.jpg)


## grid-auto-rows ,grid-auto-columns

其實直接看文件不是很清楚到底是甚麼關係，但去了解關鍵字名稱，再回來看就很清楚。

關鍵字: Grid Implicit vs Explicit Tracks

> explicit 的 grid 可以透過 grid-template-columns 和 grid-template-rows 來定義，剩餘多出來的 grid 都視為 implicit grid。
implicit grid 預設的寬度會根據內容的大小來改變，若想改變 implicit grid 的話，可以透過 grid-auto-rows 和 grid-auto-columns 來定義。

參考: [[CSS] Grid Layout | PJCHENder 未整理筆記](https://pjchender.dev/css/css-grid-layout/)

簡單來說，`grid-template` 設定出 explicit，超出`grid-template` 就是 Grid Implicit。上面的範例其實就是 Grid Implicit 。

這個網站有demo範例，[CSS Grid 筆記 05-CSS Grid Implicit vs Explicit Tracks | ShunNien's Blog](https://shunnien.github.io/2018/03/18/css-grid-05/)

![](https://i.imgur.com/jml2F4X.png)


https://developer.mozilla.org/zh-TW/docs/Web/CSS/grid-template

是將所有三個長期屬性設置為的關鍵字none，表示沒有顯式網格。沒有命名的網格區域。行和列將隱式生成；它們的大小將由 `grid-auto-rows` (en-US)和 `grid-auto-columns` (en-US)屬性確定。


## grid-item 

中文叫「Grid Item 」。主要控制所選擇大小。


![](https://i.imgur.com/KoGgv3l.png)

這邊主要要記得編號開頭順序是從`1`開始編號，column是紅色，row是綠色開始算。只要在 grid 下的item 設定`grid-column-start`~`grid-column-end`和`gird-row-start`~`grid-row-end`就可以現在畫面上

上圖使用這邊範例[CSS格線布局 - CSS | MDN](https://developer.mozilla.org/zh-TW/docs/Web/CSS/CSS_Grid_Layout)

- grid-column: xxx / xxx 或 span 2
  - grid-column-start
  - grid-column-end
-  grid-row: xxx/xxx 或 span 2
  - grid-row-start
  - grid-row-end
- grid-area: https://developer.mozilla.org/en-US/docs/Web/CSS/grid-area



### span 

```css=
  grid-row: span 2;
```

### repeat

```css=
grid-template-area: repeat(5, 1fr);
```

### minmax(100px,auto)

最小 100px 
最大 auto

> auto。auto 意味著大小會檢查內容大小，並適配這一行 cell 內最高項目的高度。

### grid-item 重疊會發生什麼事情?

grid-row/grid-column 有兩個 item 設定一樣(或撞號)不會有任何問題。
後面Element 會在 Element 上面，這邊跟順序有關係。
其實上面MDN範例就可以看出來，只是grid-template,grid-row,grid-item 都會一樣有這種狀況。

![](https://i.imgur.com/2aJlqgY.png)



## 總結

https://nicolakacha.coderbridge.io/2020/09/22/css-flex-grid-2/

我覺得這篇還滿適合當 cheet sheet ，因為算介紹滿完整又簡短，我覺得看完教學再回來看會比較好懂。
大部分網站都沒有介紹很完整，要了解需要花了一些時間，雖然寫的筆記未必很清楚，但整理資源方便下次回來看能快速熟悉。


