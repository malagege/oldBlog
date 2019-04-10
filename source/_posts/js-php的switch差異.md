---
title: "js,php的switch差異"
date: 2019-03-26 20:06:30
tags: [js, php, switch]
categories: JavaScript
---

最近 JS 有一個 bug
發現 bug 是在 switch
我是字串跟數字去做比對
就發現 js 不會批對到我想要的(跑去 default:去)
趕快來筆記!!

<!--more-->

```js
switch ("1") {
  case 1:
    console.log("1");
    break;
  case "1":
    console.log("2");
    break;
}
```

Ans: 2

```php
<?php
switch('1'){
    case 1:
        echo '1';
        break;
    case '1':
        echo '2';
        break;
}
```

Ans: 1

Google 找了一下 JS 的 switch

> Description
> Section
> A switch statement first evaluates its expression. It then looks for the first case clause whose expression evaluates to the same value as the result of the input expression (using the strict comparison, ===) and transfers control to that clause, executing the associated statements. (If multiple cases match the provided value, the first case that matches is selected, even if the cases are not equal to each other.)

裡面有提到他是用 switch 試用**三個等於**`===`

PHP 是用兩個等於，可以看官網文件[PHP: switch - Manual](https://www.php.net/manual/en/control-structures.switch.php)
