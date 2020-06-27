---
title: Email 支援 HTML 和 CSS 查詢方法
date: 2020-04-02 22:50:12
tags: [email, html, css]
categories: HTML
---

最近接到簡單的任務
修改 E-mail 排版
就是縮排的問題
但由於我對 Outlook 支援性不是很清楚
一個一個試感覺不是很方便
有找到幾個網址可以查

<!--more-->

## 查詢網站

### HTML

- [Email HTML tags & attributes ignored by Outlook](https://www.outlook-apps.com/html-ignored-by-outlook/)
- [Can I email… Support tables for HTML and CSS in emails](https://www.caniemail.com/)

### CSS

- [CSS Support Guide for Email Clients [+Checklist] | Campaign Monitor](https://www.campaignmonitor.com/css/text-fonts/svg/)
- [Email Client CSS Support | Email Design Reference](https://templates.mailchimp.com/resources/email-client-css-support/)

### 其他

神奇的樣版引擎套 email 的 html + css
[MJML - The Responsive Email Framework](https://mjml.io/)

## 處理空白問題

不知道再存文字寄送的時候，Outlook 收信時候不會轉空白字元
我在程式外面加 pre html tag就能看到縮排

另外一種方式全部轉成 HTML

[【译】使用 <pre> 标签样式的注意事项 - 奇舞团博客](https://75.team/post/considerations-for-styling-the-%3C-pre-%3E-tag.html)
[vb6 - HTML blank spaces in email - Stack Overflow](https://stackoverflow.com/questions/11429772/html-blank-spaces-in-email)

改變 ol li 寫法
[html - Can ordered list produce result that looks like 1.1, 1.2, 1.3 (instead of just 1, 2, 3, ...) with css? - Stack Overflow](https://stackoverflow.com/questions/4098195/can-ordered-list-produce-result-that-looks-like-1-1-1-2-1-3-instead-of-just-1)