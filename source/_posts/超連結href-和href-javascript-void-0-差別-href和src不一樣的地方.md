---
title: "超連結href=#和href=javascript:void(0)差別 & href和src不一樣的地方"
date: 2019-02-27 23:25:06
tags: [href, src]
categories: HTML
---

之前很常看到網頁做不做跳頁超連結都用`href="#"`
也有看過更早的程式用`javascript:void(0);`
但也沒有深入這兩個的差別
當然知道`#`有錨點功能，但說真的也不知道這樣用會有什麼缺點
[神奇的 html 錨點，讓你的網頁在內部自由的跳轉 - 每日頭條](https://kknews.cc/zh-tw/tech/ena88ry.html)
之前有看過一篇[在 Angular 套版時應注意多層次選單超連結的使用 | The Will Will Web](https://blog.miniasp.com/post/2018/09/22/Angular-template-with-hash-link-problem)
今天就小記一下，不然怕之忘了很難找

<!--more-->

## href,javascript:void(0),javascript:;

> 我們一般比較常見的寫法有幾種：
>
> - 直接使用 # 當作超連結
>   這種技巧對於靜態版型還可行，許多網頁設計師也很習慣這樣寫，但是當使用者點擊連結後，卻會在目前網址後方加上一個 # 符號。一來，我個人相當不喜歡這種作法，因為網址會發生改變；二來，這種寫法會讓 SPA (單一頁面應用程式) 的路由發生異常，所以應該避免。
> - 使用特殊的 javascript:void(0) 當作超連結
>   這種寫法通常是前端工程師以及對 UX 特別要求的設計師愛用，原因無他，就是網址列不會出現怪怪的 # 符號。這種寫法同時也對 SPA 網頁應用程式相當友善，所以推薦使用。但唯一的缺點，就是比 # 多打太多字了！
> - 使用特殊的 javascript:; 當作超連結
>   這種寫法跟 javascript:void(0) 如出一轍，但硬是少打了 6 個字元，如果學會他，你這輩子可能有機會打超過 1000 次這種連結，就可以少打 6000 個按鍵，不無小補啦。

以上來自:[在 Angular 套版時應注意多層次選單超連結的使用 | The Will Will Web](https://blog.miniasp.com/post/2018/09/22/Angular-template-with-hash-link-problem)

## src href

這之前也有看過類似文章
但今天忘了，剛好又看到有人討論這個問題
當出學 HTML 也是用記得，沒有想到這個問題

[六角學院 - 學員社團](https://www.facebook.com/groups/110635703123103/permalink/361861428000528/?comment_id=361918307994840&comment_tracking=%7B%22tn%22%3A%22R%22%7D)
[html - Difference between SRC and HREF - Stack Overflow](https://stackoverflow.com/questions/3395359/difference-between-src-and-href?fbclid=IwAR19QTM94zHMAXPawqr9i_sWKF7-UXD6b9WWRf-aLNorxSAWpctLPz2B2mc)

> 另外依據文內資訊，在載入頁面時，此兩者個運作行為會有差別。
> 具體而言，瀏覽器會依據 html 的內容依序載入元素，由於 src 本身即為元素之一，因此瀏覽器會等 src 的內容完整抓取之後才繼續載入後續元素。然而 href 只是連結，瀏覽器並不會去載入 href 的任何內容。
> 這就是爲什麼 CSS 是用 href，但是 javascript 卻是用 src
