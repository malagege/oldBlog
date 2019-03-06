---
title: "CSS Selector做空白可能要注意的事情"
date: 2019-01-08 20:39:29
tags: [css]
categories: CSS
---

最近 jQuery 使用`.siblings(':first-child + ~ ');`
感覺哪裡怪怪的
前陣子我踩了`div :hover`地雷(我心中所想的是`div:hover`)
結果差很多...
太少寫 CSS 犯了低級的錯誤....
但我今天看到這個`.siblings(':first-child + ~');`怎麼可以跑.....

<!--more-->

其實我猜測在做`~+`的時候前後空白會被忽略
因為之前不小心寫`div :hover`
我覺得正常前後不要空白比較好

雖然我還沒看到相關文件有說前後可以空白
不過自己實驗`~+`前後空白不會有這個問題

最後無聊提醒[[CSS] 不常用的 CSS Selectors 筆記 | 程式狂想筆記](https://malagege.github.io/blog/2017/04/29/logdown/2017-04-29-1767277/)
jQuery 的 siblings 跟 CSS 的`~`是不一樣的
jQuery 的 siblings 是除了自己本身 selector 同階層都會抓到
但是 CSS`xxx~`是 xxx 後面屬性都會抓到

`element1+`跟`element1~`都可以跑
好奇怪為什麼這樣還是可以跑
目前猜測後面沒寫`element1+`，會自動帶`*`
所以跟結果`element1+*`一樣
不過我還是沒有找到文章
日後看到會再補充

最後我自己又重新看 code 一次
`.siblings(':first-child + ~');`
其實我這個寫法可以這樣寫`.siblings(':first-child~');`
甚至可以用 jQuery`not`function 使用
`.siblings().not(:first-child);`
我很少用複查的寫法

這個 js 用途是用在做多列 table，點擊一個按鈕，需要把其他列資料隱藏起來
所以才用這個方法
但我最近發現 table 結構

```html
<table>
  <thead>
    <tr>
      <th>標題</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>test</td>
    </tr>
  </tbody>
</table>
```

其實這樣編排的話不需要做這麼複雜的事情
我可以對 tbody 做比較不需要寫複雜的 code
**不過說真的 thead 跟 tbody 沒寫的時候**
瀏覽器其實會幫你建立的
這通常自己會忘記(已經踩好幾次了 orz)
