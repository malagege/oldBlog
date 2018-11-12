---
title: 使用nodemailer做批次寄信
date: 2018-10-30 21:03:30
tags: [nodejs,nodemailer,mail]
categories: JavaScript
---

由於之前工單信件寫錯...
需要補寄信件給使用者
今天研究nodejs使用nodemailer發信
發現真的還滿簡單好用
說不定下次就有機會可以用到


<!--more-->

## nodemailer
[malagege/sample-code-node-mail-batch: 批次發信範例](https://github.com/malagege/sample-code-node-mail-batch)

`可注意(1)(2)(3)要改的地方`
```js
/////////(1)
const csvFilePath='data.csv'
var mail_from = 'from@xxxx.com.tw'
var subject = '標題'


var nodemailer = require('nodemailer');
const csv=require('csvtojson')
/////////(2)
var transporter = nodemailer.createTransport({
    host: 'mail_server_url',
    port: 25 ,
    secure: false,
    auth: {
        user: 'from@xxxx.com.tw',
        pass: 'password'
    }
});
csv()
.fromFile(csvFilePath)
.then((jsonObj)=>{
    // console.log(jsonObj);
    /**
     * [
     * 	{a:"1", b:"2", c:"3"},
     * 	{a:"4", b:"5". c:"6"}
     * ]
     */ 

    jsonObj.forEach(data => {
        /////////(3)
        let mail_to = data.usermail
        let email_template = `
        Hello World!!!!
        <img src="${data.pic_url}"><br>
        姓名：${data.username}<br>
        訂單編號：${data.no}<br>
        `

        var mailOptions = {
          from: mail_from,
          to: mail_to,     //寄送人
          subject: subject,
          html: email_template,
        };
        
      
      // verify connection configuration
      // transporter.verify(function(error, success) {
      //   if (error) {
      //        console.log(error);
      //   } else {
      //        console.log('Server is ready to take our messages');
      //   }
      // });
      
      transporter.sendMail(mailOptions, function(error, info) {
        if (error) {
          console.log('mail_to:' + mail_to)
          console.log(error);
        } else {
          console.log(mail_to + ' , Email sent: ' + info.response);
        }
      });
      
      });

})
 
```

另外，附加檔案可以參考[sample-code-node-mail-batch/test_add_file.js at master · malagege/sample-code-node-mail-batch](https://github.com/malagege/sample-code-node-mail-batch/blob/master/test_add_file.js)


程式參考來源:
[Nodejs发邮件组件Nodemailer | 粉丝日志](http://blog.fens.me/nodejs-email-nodemailer/)  {% asset_link web_bk.png 備份圖 %}


## preview-email

https://www.npmjs.com/package/preview-email

可以簡單看預覽信件內容
雖然我覺得可以用寫死一個信箱看...
缺點好像附加檔案cid會抓不到

```js
const previewEmail = require('preview-email');
...
previewEmail(message).then(console.log).catch(console.error);
//transport.sendMail(message).then(console.log).catch(console.error);
```
message跟nodemailer搭配



最後，email-templates我就沒研究了
看了文件感覺很複雜
我就先用程式邏輯就可以跑出我想要的



