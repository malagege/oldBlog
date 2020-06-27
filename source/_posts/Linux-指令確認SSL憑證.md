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

openssl s_client -connect server.yourwebhoster.eu:21 -starttls ftp

Test POP3 certificate

openssl s_client -connect server.yourwebhoster.eu:995

Test IMAP certificate

openssl s_client -connect server.yourwebhoster.eu:993

Test SMTP SSL certificate

openssl s_client -connect server.yourwebhoster.eu:465

Test SMTP TLS certificate

openssl s_client -connect server.yourwebhoster.eu:587 -starttls smtp

Test HTTPS certificate

openssl s_client -connect server.yourwebhoster.eu:443

Test DirectAdmin certificate

openssl s_client -connect server.yourwebhoster.eu:2222


----

How to verify SSL certificates with SNI (Server Name Indication) using OpenSSL

Using SNI with OpenSSL is easy. Just add the -servername flag and you are good to go. Replace in the examples below mail.domain.com with the SNI name. Note: you can also use the SNI name to replace server.yourwebhoster.eu with.

Test FTP certificate

openssl s_client -connect server.yourwebhoster.eu:21 -starttls ftp -servername mail.domain.com

Test POP3 certificate

openssl s_client -connect server.yourwebhoster.eu:995 -servername mail.domain.com

Test IMAP certificate

openssl s_client -connect server.yourwebhoster.eu:993 -servername mail.domain.com

Test SMTP SSL certificate

openssl s_client -connect server.yourwebhoster.eu:465 -servername mail.domain.com

Test SMTP TLS certificate

openssl s_client -connect server.yourwebhoster.eu:587 -starttls smtp -servername mail.domain.com

Test HTTPS certificate

openssl s_client -connect server.yourwebhoster.eu:443 -servername mail.domain.com
