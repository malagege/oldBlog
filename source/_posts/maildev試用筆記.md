---
title: maildev試用筆記
date: 2018-12-09 22:19:32
tags: [nodejs,mail]
categories: Nodejs
---

最近在FB社群看到有人分享fake stmp server
[社群連結](https://www.facebook.com/groups/javascript.tw/permalink/1620946454673353/)
之前有測試nodemailer
但是缺少附加檔案可以測試
[preview-email - npm](https://www.npmjs.com/package/preview-email)無法做到
想說測試這個能不能用

<!--more-->

線上服務
[Ethereal Email](https://ethereal.email/?fbclid=IwAR1_1vsbOR2U5V3MeFFCyNAvvbkh4gVQxtyif-oJZrhDTdtFQMdUmAKLuG8)
[Mailtrap.io — Fake smtp testing server. Dummy smtp email testing](https://mailtrap.io/)
不過...，這兩個不是這文章的重點


## maildev 安裝

官網[MailDev - Create better email during development](https://danfarrelly.nyc/MailDev/?fbclid=IwAR26sE6dCMfjn9xeNhWJh_nLfw3cTiPu-pr5F9nd0qJyykPCaI7ihQDBR-8)寫了很清楚

### npm安裝

```
  $ npm install -g maildev
  $ maildev
```
### docker執行

```
  $ docker pull djfarrelly/maildev
  $ docker run -p 1080:80 -p 1025:25 djfarrelly/maildev
```

### 原始檔案暗樁

```
  $ git clone git@github.com:djfarrelly/MailDev.git
  $ npm install -g ./maildev
  $ maildev
```

## 安全憑證記得要關掉

```js
  const transport = nodemailer.createTransport({
    port: 1025,
    ignoreTLS: true,
    // other settings...
  });

```

### 程式碼執行

拿[malagege/sample-code-node-mail-batch: 批次發信範例](https://github.com/malagege/sample-code-node-mail-batch)test_add_file.js
修改
主要要特別注意
* ignoreTLS: true
* auth要拿掉
* cid 在多個寄信的時候會在寄信的時候會有問題....
明明個別設定1.jpg,2.jpg，但是結果兩封信卻都是2.jpg
最後用`cid:00000001${data.img}`方式解決...
但感覺是maildev問題...

以下code也沒先commit到github上面
```js
/////////(1)
const csvFilePath='data.csv'
var mail_from = 'from@xxxx.com.tw'
var subject = '標題'


var nodemailer = require('nodemailer');
const csv=require('csvtojson')
/////////(2)
var transporter = nodemailer.createTransport({
    host: 'localhost',
    port: 1025 ,
    ignoreTLS: true,
    // auth: {
    //     user: 'from@xxxx.com.tw',
    //     pass: 'password'
    // }
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
        Hello World!!!!${data.img}
        <img src="cid:00000001${data.img}"/><br>
        姓名：${data.username}<br>
        訂單編號：${data.no}<br>
        `

        let mailOptions = {
          from: mail_from,
          to: mail_to,     //寄送人
          subject: subject,
          html: email_template,
          attachments: [{
            filename: data.img,
            path: data.img,
            cid: '00000001'+data.img
          }]
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