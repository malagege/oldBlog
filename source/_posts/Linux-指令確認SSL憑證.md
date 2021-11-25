---
title: Linux 指令確認SSL憑證
date: 2020-05-30 01:52:35
tags:
categories: Linux
---

單純紀錄

<!--more-->

TODO: 有空回來整理

Test FTP certificate
```
openssl s_client -connect server.yourwebhoster.eu:21 -starttls ftp
```
Test POP3 certificate
```
openssl s_client -connect server.yourwebhoster.eu:995
```
Test IMAP certificate
```
openssl s_client -connect server.yourwebhoster.eu:993
```
Test SMTP SSL certificate
```
openssl s_client -connect server.yourwebhoster.eu:465
```
Test SMTP TLS certificate
```
openssl s_client -connect server.yourwebhoster.eu:587 -starttls smtp
```
Test HTTPS certificate
```
openssl s_client -connect server.yourwebhoster.eu:443
```
Test DirectAdmin certificate
```
openssl s_client -connect server.yourwebhoster.eu:2222
```

----

How to verify SSL certificates with SNI (Server Name Indication) using OpenSSL

Using SNI with OpenSSL is easy. Just add the -servername flag and you are good to go. Replace in the examples below mail.domain.com with the SNI name. Note: you can also use the SNI name to replace server.yourwebhoster.eu with.

Test FTP certificate
```
openssl s_client -connect server.yourwebhoster.eu:21 -starttls ftp -servername mail.domain.com
```
Test POP3 certificate
```
openssl s_client -connect server.yourwebhoster.eu:995 -servername mail.domain.com
```
Test IMAP certificate
```
openssl s_client -connect server.yourwebhoster.eu:993 -servername mail.domain.com
```
Test SMTP SSL certificate
```
openssl s_client -connect server.yourwebhoster.eu:465 -servername mail.domain.com
```
Test SMTP TLS certificate
```
openssl s_client -connect server.yourwebhoster.eu:587 -starttls smtp -servername mail.domain.com
```
Test HTTPS certificate
```
openssl s_client -connect server.yourwebhoster.eu:443 -servername mail.domain.com
```

讀取網站憑證指紋

```
echo | openssl s_client -connect www.cgmh.org.tw:443 |& openssl x509 -fingerprint -noout
```

網站憑證鏈指紋

```
 echo "" | openssl s_client -showcerts \
                             -connect saucelabs.com:443 2>&1 | \
    sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p;
             /-END CERTIFICATE-/a\\x0' |\
    sed -e '$ d' | xargs -0rl -I% sh -c "echo '%' | \
    openssl x509 -fingerprint -noout -sha256 -subject"
```
參考[sni - Different SHA1 fingerprint in browser and openssl - Stack Overflow](https://stackoverflow.com/questions/48298302/different-sha1-fingerprint-in-browser-and-openssl)
但根憑證好像算錯了?

sha1,sha256,md5


    SHA-256

```
    openssl x509 -noout -fingerprint -sha256 -inform pem -in [certificate-file.crt]
```
    SHA-1

```
    openssl x509 -noout -fingerprint -sha1 -inform pem -in [certificate-file.crt]
```
    MD5

```
    openssl x509 -noout -fingerprint -md5 -inform pem -in [certificate-file.crt]
```
[How to view a certificate fingerprint as SHA-256, SHA-1 or MD5 using OpenSSL for RSA Authentication ... - RSA Link - 4230](https://community.rsa.com/t5/securid-access-knowledge-base/how-to-view-a-certificate-fingerprint-as-sha-256-sha-1-or-md5/ta-p/4230)

[Certificate Decoder - Decode certificates to view their contents](https://www.sslshopper.com/certificate-decoder.html)

[SSL 相關的測試工具 – 軟體品管的專業思維](https://www.qa-knowhow.com/?p=3888)

[SSLScan - 瞭解網站是否安全的好用小工具](http://dog0416.blogspot.com/2020/11/sslscan.html)

[OpenSSL command cheatsheet](https://www.freecodecamp.org/news/openssl-command-cheatsheet-b441be1e8c4a/)
