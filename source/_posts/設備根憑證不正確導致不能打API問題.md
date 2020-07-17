---
title: 設備根憑證不正確導致不能打API問題
date: 2020-07-14 22:26:12
tags: [tls,憑證]
categories: Linux
---

最近要上線的專案會用到 LINE WEBHOOK
竟然上正式環境 line 後台 webhook 模擬可以觸發 API
但使用行動裝置竟然不可以打...(access.log沒有收到訊息)
我們公司有防火牆、Server Load Balancer...
查詢上花了不少時間

<!--more-->

由於流程很多，我上面就直接講重點

## 檢查方向

### LINE 文章有提到注意事項

> 在設定HTTPS伺服器時，有下列幾點必須注意的事項：
>
>    1. HTTPS伺服器所使用的根憑證（Root CA）必須是在LINE平台的白名單列表中，否則LINE平台會拒絕傳送訊息。在白名單列表中大多數的憑證都需要付費申請，但是LINE平台也支援常用的免費憑證，例如Let’s Encrypt。
>    2. 請勿使用已知具有安全性漏洞的協定（例如SSL v2或SSL v3）或Cipher Suite（例如SWEET32或CVE-2016-2183）。
>    3. 請務必正確設定中繼憑證（Intermediate certificate），以避免無法對應到根憑證而發生錯誤。這是最常見的問題通報狀況，請在設定HTTPS伺服器時多加留意。

[開發LINE聊天機器人不可不知的十件事 - LINE ENGINEERING](https://engineering.linecorp.com/zh-hant/blog/line-device-10/)

我們公司瀏覽器看網頁沒有跳出安全性問題，Android、Iphone、Linux 也沒有問題
所以第一項認為沒問題，但魔鬼出在細節裡
文章裡的根憑證白名單列表看不見
**Google 一下會發現 TWCA 沒有這個憑證，後面再詳細說明**
[developers.line.biz/media/messaging-api/build-bot/ca_root_v2.txt](https://developers.line.biz/media/messaging-api/build-bot/ca_root_v2.txt)
總之我們前面認為這一段沒有問題

2,3 憑證都確認都沒有問題

[SSL Certificate Comparison and Reviews](https://www.sslshopper.com/)

這個網站就可以檢查憑證是否有問題

### Load Balance SNI 設定

當我們憑證出問題的時候
網路同仁有打電話給 Load Balance 廠商詢問設定
廠商提到要設定 SNI
讓我想到之前驗證環境的問題
[Java 因憑證 SNI 問題驗證不過 | 程式狂想筆記](https://malagege.github.io/blog/2020/06/25/Java-%E5%9B%A0%E6%86%91%E8%AD%89-SNI-%E5%95%8F%E9%A1%8C%E9%A9%97%E8%AD%89%E4%B8%8D%E9%81%8E/)
原來 Apache 做的設定，對應 LB(負載平衡)機器也要做這些設定
但遺憾還是沒有解決問題

### 根憑證是否為裝置(Client)認證

前面第一點已經據透了
我們發現驗證環境的萬用憑證可以跑
但是正式環境驗證憑證不可以跑

原來公司舊有憑證是四層(TWCA)
但是新憑證是三層(TWCA)
這2種都是合法憑證

{% asset_img l4.png 四層 %}

從憑證路徑可以看到一個三層，一個四層
但偏偏 LINE ROOT CA 只有`CN=TWCA Root Certification Authority, OU=Root CA, O=TAIWAN-CA, C=TW`
沒有 TWCA ROOT CA

公司剛好因為換新憑證都改用三層
所以會有這個問題...

所以中繼憑證改成四層就沒這個問題

1. 完美成果

{% asset_img cgh.png "" %}

{% asset_img cgh2.png "2" %}

2. root ca 包在中繼憑證，順序錯誤

{% asset_img eci.png "" %}

{% asset_img eci2.png "" %}

3. root ca 包在中繼憑證+中繼憑證順序錯誤

{% asset_img linewebhook.png "" %}

{% asset_img linewebhook2.png "" %}

我的建議憑證包四層比較不會出問題
因為第二層憑證是一樣的
第三層的話，Root CA 就會抓到白名單

假如四層的話，中繼串到第三個後面，就是白名單
不過 TWCA 說最新發的都是三層??這原因我就沒深入追查

題外話，這邊我發現有趣現象
用 Firefox 是三層(沒有看到四層)，Chrome(IE,Window)是四層
不知道是不是憑證是用 名稱去找的 ~~**這個是我猜的，目前沒有找到資訊**~~
> 憑證簽發者(ISSUER)的名稱與憑證主體(Subject Name)名稱相同!
參考:[憑證串鍊的解釋 - SSL憑證推薦網 | SSL憑證 | code Sign](https://www.sslbuyer.com/index.php?option=com_content&view=article&id=183:what-is-certificate-chain&catid=25&Itemid=4031)
難道真的是看名字??
Android也是三層
剛好有抓到 TWCA Root CA 根憑證所以才三層
至於怎麼抓的就不研究了

現在回想[萬用憑證(WildCard)和中間憑證小記 | 程式狂想筆記](https://malagege.github.io/blog/2018/10/10/%E8%90%AC%E7%94%A8%E6%86%91%E8%AD%89-WildCard-%E5%92%8C%E4%B8%AD%E9%96%93%E6%86%91%E8%AD%89%E5%B0%8F%E8%A8%98/)
是不是沒有串到根憑證??現在也查不到了

### 檢查 Chipser

* [那些關於SSL/TLS的二三事(九) — SSL (HTTPS)Communication | by Carl | Medium](https://medium.com/@clu1022/%E9%82%A3%E4%BA%9B%E9%97%9C%E6%96%BCssl-tls%E7%9A%84%E4%BA%8C%E4%B8%89%E4%BA%8B-%E4%B9%9D-ssl-communication-31a2a8a888a6)

* [Apache進階設定(1) - TLSv1.2 TLSv1.1 SSLv2 SSLv3 - BrilliantCode.net](https://www.brilliantcode.net/1229/apache-advance-settings-tlsv1-2-tlsv1-1-sslv3/)

之前有聽說可以不戴加密做 TLS
有看到這個關鍵字 null cipher suites
不知道是不是指這個?
我就不詳細研究了

## 名詞整理

### 根憑證

> Self-Signed CA
> 亦可稱為 root CA。由於此種 CA 所發的憑證(certificate)是不經由任何上層 CA 所認證，而是以「自行認證」的方式進行認證。因此像是最上層的商業 CA，或是自行架設內部認證用的 CA，都可以屬於此類。
參考來源: [Certificate Authority(CA) 憑證簡介 @ MISTECH 技術手抄本 :: 痞客邦 ::](https://mistech.pixnet.net/blog/post/80751019-certificate-authority%28ca%29-%E6%86%91%E8%AD%89%E7%B0%A1%E4%BB%8B)

簡單來說就是根憑證，一開始你的電腦微軟附的作業系統裡面，有裝一些憑證，隨著系統更新，這些憑證都會update。
根憑證因為沒有上層，所以你電腦有根憑證白名單，就會算是合法的憑證。

### 中繼憑證


{% asset_img chain.png title %}
參考來源:[那些關於SSL/TLS的二三事(十二) — Chain of Trust | by Carl | Medium](https://medium.com/@clu1022/%E9%82%A3%E4%BA%9B%E9%97%9C%E6%96%BCssl-tls%E7%9A%84%E4%BA%8C%E4%B8%89%E4%BA%8B-%E5%8D%81%E4%BA%8C-chain-of-trust-f00da1f2cc15)
這篇說得很清楚，但我也只是略懂略懂
一直在想為什麼會有中繼憑證，缺少中繼憑證會發生什麼事?順序錯會發生什麼事?
當前憑證並非是 Root CA 認證而是靠中繼憑證...
中繼憑證會再往上根 Root Ca 驗證
到白名單就是安全性任憑正

### 憑證加密過程

![](https://upload.wikimedia.org/wikipedia/commons/thumb/6/65/PublicKeyCertificateDiagram_It.svg/langzh-1100px-PublicKeyCertificateDiagram_It.svg.png)

簡單說就是 `公鑰` + `上層憑證私鑰` = 憑證
這張憑證就會給你設定 Apache 上
驗證憑證會跟上層憑證公鑰解開加密確認值
一致就確認沒問題，繼續往上面憑證追查，直到 Root CA


### 整理憑證鍊遇到問題

#### Contains anchors

包到主憑證，之前有看過文章中繼憑證不需要包 root ca

[Chain issues: Contains anchors](https://qualys-secure.force.com/discussions/s/question/0D52L00004TnvJ5SAJ/chain-issues-contains-anchors)
[Trouble with intermediate certificate "Chain Issues: Incomplete"](https://qualys-secure.force.com/discussions/s/question/0D52L00004TnyFQSAZ/trouble-with-intermediate-certificate-chain-issues-incomplete)

#### Extra download

好像就是中繼憑證跟給予憑證沒包在一起樣子?
我沒有實驗，像 nginx 就沒有中繼憑證設定
不是很確定原因

[How to fix "Extra download"](https://qualys-secure.force.com/discussions/s/question/0D52L00004TnuTuSAJ/how-to-fix-extra-download)
[Multiple Certification Paths](https://qualys-secure.force.com/discussions/s/question/0D52L00004TnuRQSAZ/multiple-certification-paths)
https://www.ssl.com.tw/Down/



### 下載憑證方法(無法看到中繼憑證)

[Get SSL Certificate from Server (Site URL) - Export & Download - ShellHacks](https://www.shellhacks.com/get-ssl-certificate-from-server-site-url-export-download/)

```bash
# 下載憑證 (不包含中繼)
echo | openssl s_client -servername NAME -connect HOST:PORT |\
  sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > certificate.crt
# 查看中繼憑證 (-A 數字可以大一點)
 echo ""|openssl s_client -connect www.cgh.org.tw:443 2>&1 | grep -A 9 "Certificate chain"
```
其實之前就有做過類似筆記:[Linux 指令確認SSL憑證 | 程式狂想筆記](https://malagege.github.io/blog/2020/05/30/Linux-%E6%8C%87%E4%BB%A4%E7%A2%BA%E8%AA%8DSSL%E6%86%91%E8%AD%89/)

### 格式轉換

[openssl 指令 command line - 轉檔 pem/der/p7b/pfx/cer | SSORC.tw](https://ssorc.tw/7142/openssl-%E6%8C%87%E4%BB%A4-command-line-%E8%BD%89%E6%AA%94-pem-der-p7b-pfx-cer/)





### 其他

安裝中繼憑證
* [Yy's Program: GCA政府憑證串鍊(Certificate chain)問題](http://yy-programer.blogspot.com/2019/02/gcacertificate-chain.html)


see chain 
* [ssl - show entire certificate chain for a local certificate file - Stack Overflow](https://stackoverflow.com/questions/18733383/show-entire-certificate-chain-for-a-local-certificate-file)
* [Show the certificate chain of a local X509 file - kdecherf ~ %](https://kdecherf.com/blog/2015/04/10/show-the-certificate-chain-of-a-local-x509-file/)
這邊是做憑證驗證

憑證教學
* [SSL 憑證申請、取得及安裝 @ 來吧~~~電腦 :: 隨意窩 Xuite日誌](https://blog.xuite.net/towns/hc/556816027-SSL+%E6%86%91%E8%AD%89%E7%94%B3%E8%AB%8B%E3%80%81%E5%8F%96%E5%BE%97%E5%8F%8A%E5%AE%89%E8%A3%9D)

* [Apache Nginx上安裝 TWCA憑證 - Alvin Chen Club](http://www.alvinchen.club/2018/09/27/apache-nginx%E4%B8%8A%E5%AE%89%E8%A3%9D-twca%E6%86%91%E8%AD%89/)

* [Mozilla SSL Configuration Generator](https://ssl-config.mozilla.org/#server=apache&version=2.4.41&config=intermediate&openssl=1.1.1d&guideline=5.4)
之後相關 Apache, Redis, Mysql  TLS 加密可以看這邊設定

## 小記

原本這個問題沒有頭緒，line webhook 後臺可以打得到AP
但是用手機 LINE APP 操作就抓不到
有用防火牆看到 Server Action RST
正常Action:Session close
後來用公司 Load Balancer 作風包解析(tcpdump)
發現這一塊 https 三項交握有問題

看了這篇除了驗證憑證，那註銷後的憑證要怎麼確認
Google 一下找到 CRL([寫程式是良心事業: OCSP & CRL 介紹](http://ijecorp.blogspot.com/2016/01/ocsp-crl.html))
這邊就不先繼續研究了

我覺得之後可以手動做做看

TODO: 之後可能照保哥做做看自簽名憑證

這篇算是我的筆記，也許會有很多寫錯，建議大家還是看看大神寫文章!!
[購買與安裝 SSL 憑證完全攻略（以 IIS7 為例） | The Will Will Web](https://blog.miniasp.com/post/2010/03/02/The-Complete-Guide-for-Purchase-Install-SSL-Certificate-to-IIS7)
[如何使用 OpenSSL 建立開發測試用途的自簽憑證 (Self-Signed Certificate) | The Will Will Web](https://blog.miniasp.com/post/2019/02/25/Creating-Self-signed-Certificate-using-OpenSSL?fbclid=IwAR3xS1xqkOfLQmK22bDyp9bgOJ71PBNChr-2REMq3oIMT5-0biUTJl41a1k)
[免費申請 StartSSL™ 個人數位簽章與網站 SSL 憑證完全攻略 | The Will Will Web](https://blog.miniasp.com/post/2013/01/09/The-Complete-Guide-Free-StartSSL-personal-and-web-site-ssl-tls-certificates)
