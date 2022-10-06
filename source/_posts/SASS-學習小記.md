---
title: SASS 學習小記
date: 2019-06-02 18:41:31
tags: [css]
categories: SASS
---

最近上課 SASS
這邊記入我可能會忘記的東西

<!--more-->

## SMACSS

重點 擴充、模組化

- Base
  不要下 class ，可以用基本樣式
  這個地方可參考[Pure](https://purecss.io/)設計方法
- Layout
  l-grid,l-list
  (建議不要用語意命名)
  每一頁會用到的 class
- module
  每頁重複會用到的元件。如按鈕(.btn)
  錯誤範例如下

```
.btn
.btn-blue
.btn-blue-large
```

正確比要適合用 bootstrap 命名

```
.btn
.btn-primary
```

> 增加子模組
> .l-grid
> .l-grid h2 ==> .l-grid_title
> .l-grid ul ==> .l-grid_list

簡單我對 SMCASS 總結，就是做`程式切分結構`
可參考如下網站
[鐵人賽 20 - Sass 資料夾結構 | 卡斯伯 Blog - 前端，沒有極限](https://wcc723.github.io/css/2016/12/20/sass-folder/)

1. base
2. layout
3. module
   ------以下可看專案大小決定
4. theme

...

更多可參考這篇[Sass 7-1 Pattern](https://gist.github.com/rveitch/84cea9650092119527bc)

## OOCSS

1. 結構與樣式分離
2. 容器與內容分離

首先，還是先分清楚什麼是結構?什麼事容器?

### 1.結構、樣式

**結構**
EX: Bootstrap .btn
.btn 這個就是結構

**樣式**
.btn-lg .btn-primary
這些就是樣式

### 2.容器、內容

**容器**
EX: Bootstrap 的.container , col-3
簡單就是排版、網格

**內容**
EX: Bootstrap 的 .pagination .list-group

### OOCSS 好處

1. 增加使用性(reuse)
2. 減少 code

需要注意的事情!!
`樣式`CSS 不要跟 `結構`一起寫!!!!
**錯誤範例**EX: .col-3 .btn
這樣會失去彈性

容器與內容可以玩一下裡面的排版
正常容器裡面可以放`容器`和`內容`
完一下可以大概知道這兩個差異
[LayoutIt! - Interface Builder for CSS Grid and Bootstrap](https://www.layoutit.com/cn)

### 我們可能有寫過的 OOCSS

以前我看到有些網站的 CSS 是這樣寫的
ex: .text-left, .mt1, .mt2, .mt3 ...
覺得這樣寫也不錯，後來我有些網站也有這樣寫

### OOCSS 會有什麼缺點

不好的話，html 的 class 東西可能會越來越多
這時候我覺得可以看情況是不是需要用 module

## BEM

BEM 的意思是區塊（Block）、元素（Element）、修飾符（Modifier）

### Block

這邊覺得簡單講就是一個 module(模組)
EX: .card,.list\_\_item

`__`這邊規定說只能放一個，能放多個

**這邊有一個重點**

### Element

一個 Block 可以放多個 Element。我簡單說他是`子模組`。
在使用 Element

.list\_\_item\_\_img
.list\_\_item\_\_title
.list\_\_item\_\_content

### Modifier

簡單就是說(子)模組狀態
正常會在 Modifier 放`--`

### 符號\_\_跟-- 沒有強制規定

BEM 符號可以看公司規定做調整
最近剛好看到巴哈論壇他們的 CSS 好像是這樣設計的
但他們符號沒有跟 BEM 用一樣

## 設計模式總結

最後紀錄三個設計 CSS 設計模式
設計不一定要同時用三個
選擇上可以參考上面三個設計

## 其他紀錄

### CSS 盡量不要超過 3 層

EX: .header nav ul li a

這樣太多層了

可以改成 nav li a

原因效能上也會有關係，管理上也比較麻煩

### 為什麼有些.container 裡面會包.wrap?

有時候.wrap 裡面某些元件可能需要做致中
可以搭配.wrap 操作置中
會比較方便

### 盡量不要寫 a.logo

.header .logo a
.header a.logo

寫 CSS 不要指定 html tag 跟 class 連在一起
這樣有失彈性

這上面還有一個問題
logo 不可能出現 header 上面
所以可以用

### SASS 使用變數技巧

不要用`語意`
錯誤範例 EX: .booksList,.carsList
這樣明明這些都是一樣東西
但每頁應該可以套同一個 CSS class
如: .list, .l-list(可以家前綴)

使用變數技巧

```sass
$red: red;
$body-color: $red;
```

這樣網站在改顏色的時候非常好調整
可以參考 bootstrap 原碼
[bootstrap/\_variables.scss at master · twbs/bootstrap](https://github.com/twbs/bootstrap/blob/master/scss/_variables.scss)

**語意的部分還是交給 html 吧**

backimage 圖片沒有重複使用，也不要用成變數
除非有特別關係(通常應該不會)

利用變數技巧一次產生多個 class 方法
[bootstrap/\_utilities.scss at master · twbs/bootstrap](https://github.com/twbs/bootstrap/blob/master/scss/_utilities.scss)

## 結構辨別的種類

1. 佈局 layout、頁面 page (只出現一處)
2. 工具 helper、utilities
3. 模組 module (出現兩處以上)
4. 容器 container、grid

## 其他寫 CSS 可能需要注意的事情

### float 做響應式可能需要注意的事

.fl{
float:left;
}

但需要注意到手機全版建議取消 float

### 少指定 width,height 數值

使用格線系統具有彈性

### 其他使用 SASS 技巧

#### 容器單一寫 CSS

錯誤範例
ex:
.col-3 .col-12
.col-12 .img

正確寫法

.col-12
.img

####前綴可以對應放置 SASS 資料夾
.p-about 放在 page
.l-list 放在 layout
再找 CSS 位置會特別直覺

#### 使用 page 做每頁分類

不要寫出多份 SASS(產生多個 css ex:about.css home.css)
只需要寫一個 SASS(產生一個 css ex:all.css)
這個好處在拆分 css 模組化會比較有幫助

#### page module 比較

page vs module
page 只有當頁有用到
module 有很多頁會用到

#### 不知道歸類方法

在不知道要放在哪邊情況下
可以先放在`inbox`資料夾
方便以後知道後再做移轉

#### 最後思考

是否能將 Layout 與 Page 重構為 module?

先記錄這樣...，我還有格線系統沒紀錄 orz
