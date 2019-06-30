---
title: PHP 用 AES 加密解密結果作簽章不一樣 (PKCS) & 一些加密雜記
date: 2019-06-15 15:08:34
tags: [php, aes]
categories: PHP
---

最近同事搞不定 PHP7 AES 加密
由於我們從 PHP5 升級到 PHP7
我們換成 openssl 但是到 Android APP 解憑證的時候竟然驗證結果會不一樣??
被同事抓去救火(同事也處理不行 XD)
然後不熟加密原理的我
第一件事情就是去 github 上面找 [ChaoLiFL/PHP-Java-AES-Encrypt-Decrypt: Pass data AES encrypted between PHP 5.x, PHP 7.1, PHP 7.2 and Java. Support two ways of encryption and decryption: Base64, Hexadecimal.](https://github.com/ChaoLiFL/PHP-Java-AES-Encrypt-Decrypt)
沒想到剛好可以用!!! (運氣好)
不過還是卡在 簽章 結果會不一樣

<!--more-->

## AES 一些加密範例

今天又找了幾個 AES 加密

- [lyhiving/aes: 兼容 php、js、JAVA 的 AES 加密](https://github.com/lyhiving/aes)
- [pkuoliver/EasyAES: AES encrypt/decrypt, Android, iOS, php compatible(兼容 php, Android, iOS 平台)](https://github.com/pkuoliver/EasyAES)
- [simplephp/php-java-AES-128-ECB: PHP && JAVA AES/ECB/PKCS5Padding 相互加解密](https://github.com/simplephp/php-java-AES-128-ECB)
- [stevenholder/PHP-Java-AES-Encrypt: Pass data AES encrypted between PHP and Java.](https://github.com/stevenholder/PHP-Java-AES-Encrypt)

## 當日狀況

首先紀錄，先整理那天的狀態
原本我同事會先做 簽章 → AES 加密
他們做簽章 check 都通過
但是到 AES 一直失敗
後來他們使用 openssl 一直試 AES
我用了 [ChaoLiFL/PHP-Java-AES-Encrypt-Decrypt: Pass data AES encrypted between PHP 5.x, PHP 7.1, PHP 7.2 and Java. Support two ways of encryption and decryption: Base64, Hexadecimal.](https://github.com/ChaoLiFL/PHP-Java-AES-Encrypt-Decrypt) 可以用了
(但可能沒有注意 console 後面有空白)
魔鬼總是藏在細節!!

後來套用我那個加密，發現 APP 做簽章反查竟然不正確??
這時候我同事發現後面有空白!!!
原因是這個導致最簽章反查導致不正確
但是 PHP 內容印出來也不正確

## 簽章

首先講一下，我目前認知簽章是怕內容有被串改
所以正常近來如如內容 array 會改他做 `json_encode`
在做 base64_url_encode(內容)
會做 hash_hmac [PHP: hash_hmac - Manual](https://www.php.net/manual/en/function.hash-hmac.php)

- [php 中使用 hash_hmac 函数实现 HMAC-SHA1 签名算法的来龙去脉 - dengjiexian123 的专栏 - CSDN 博客](https://blog.csdn.net/dengjiexian123/article/details/53313913)
- [加密和雜湊有什麼不一樣？ | Just for noting](https://blog.m157q.tw/posts/2017/12/25/differences-between-encryption-and-hashing/)
- [Code Sign - 程式碼數位簽章](https://gist.github.com/frankyueh/3d5d45236339ff0097a7)
- [TIPS-用 URL 傳送 Base64 編碼-黑暗執行緒](https://blog.darkthread.net/blog/base64-in-querystring/)

這時候還會做 base64 url encode(簽章)

> why 用 base64 url encode ?
> http://xxxx/?token=xxxxxxx
> 這時候不做 base64 url encode，可能就會有問題

~~這之間可能可以去換一些符號~~ (+/改為\*-或.\_) 如上補充 url 不能放+/

- [PHP 處理 Base64 URL 的編碼、解碼方式 | Tsung's Blog](https://blog.longwin.com.tw/2018/12/php-base64-url-encode-decode-2018/)
- [JSON Web Token 入门教程 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html)

裡面也有提到 base64 url encode
PS:[Base64 - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/Base64)，原因為何我也不確定，目前猜測可能是怕放在 url 上面??

這時候內容會是`簽章`+ `.(間隔符號)`+`內容`
出來的時候我們會用 AES 加密
這時候因為是 binary，所以還會做 Base64(也可以用自己去換一些符號)

APP 端接收到要做檢查
首先解 base64 url decode~~(或自訂反解 fucntion)~~
去做 AES 解密
這時候會是 binary(但是 PHP 出來是 string?) 這邊可能還要確認
我們要再做 base64 url decode

> 這邊講詳細一點
> `簽章`+ `.(間隔符號)`+`內容`
> 這時候可以做反簽章
> 簽章跟內容是分開獨立各做`base64 url decode`

內容解出來，在自己做 `hash_hmac`
可以得到自己`簽章內容`

這時候跟風包給的簽章內容有沒有一致?
一致就是這個資料沒有被串改

**簽章講完了，重頭戲要開始了**

## 為什麼送到 Android 有奇怪的空白字元??(PKCS7)

是 PKCS7 的關係

簡單說明一下，因為 AES 加密必須是 16 倍數(應該= =|||，是聽到這個原因)
當你資料沒有給到一定的數字，它會自動幫你補

我們當下用 workaround 方式先去解在我們資料帶`===`講好符號先去解取
但最佳解不是這樣做

- [关于 mcrypt_encrypt 和 openssl_encrypt 加密结果不一致的解决 – 继续生活](https://www.heylc.com/fuanyuopenssl.html)
- [PHP 由 mcrypt 扩展加密改为 openssl 扩展加密|xiaolingzi's blog | 生活是多彩的 | Life is colorful](https://www.xxling.com/blog/article/3114.aspx)
- [php 遷移 Mcrypt 至 OpenSSL 加密算法的詳細介紹（代碼示例） - 每日頭條](https://kknews.cc/zh-tw/other/9pgvlpl.html)
- [对称加密算法的 PKCS5 和 PKCS7 填充 | Zhiwei Li](https://zhiwei.li/text/2009/05/17/%E5%AF%B9%E7%A7%B0%E5%8A%A0%E5%AF%86%E7%AE%97%E6%B3%95%E7%9A%84pkcs5%E5%92%8Cpkcs7%E5%A1%AB%E5%85%85/)

```php
/**
 * PKCS7Encoder class
 *
 * 提供基于PKCS7算法的加解密接口.
 */
class PKCS7Encoder
{
	public static $block_size = 32;


	/**
	* 对需要加密的明文进行填充补位
	* @param $text 需要进行填充补位操作的明文
	* @return 补齐明文字符串
	*/
	function encode($text)
	{
		$block_size = self::$block_size;
		$text_length = strlen($text);
		//计算需要填充的位数
		$amount_to_pad = $block_size - ($text_length % $block_size);
		if ( $amount_to_pad == 0 )
			$amount_to_pad = $block_size;
		//获得补位所用的字符
		$pad_chr = chr($amount_to_pad);
		$tmp = "";
		for ( $index = 0; $index < $amount_to_pad; $index++ )
			$tmp .= $pad_chr;
		return $text . $tmp;
	}


	/**
	* 对解密后的明文进行补位删除
	* @param decrypted 解密后的明文
	* @return 删除填充补位后的明文
	*/
	function decode($text)
	{
		$pad = ord(substr($text, -1));
		if ($pad < 1 || $pad > self::$block_size)
			$pad = 0;
		return substr($text, 0, (strlen($text) - $pad));
	}
}
// ---------------------
// 作者：qq_34089779
// 来源：CSDN
// 原文：https://blog.csdn.net/qq_34089779/article/details/79066961
// 版权声明：本文为博主原创文章，转载请附上博文链接！
```

- [PKCS7 算法基于 php 的加解密 - qq_34089779 的博客 - CSDN 博客](https://blog.csdn.net/qq_34089779/article/details/79066961)

```php
function PKCS7Padding($str, $block_size) {
    $padding_char = $block_size - (strlen($str) % $block_size);
    $padding_str = str_repeat(chr($padding_char),$padding_char);
    return $str.$padding_str;
}
function PKCS7UnPadding($str) {
    $char=substr($str,-1,1);
    $num=ord($char);
    if($num>0 && $num <= strlen($str)) {
        $str = substr($str, 0, -1 * $num);
    }
    return $str;
}
```

- [PHP 加解密算法使用 openssl 替换 mcrypt 扩展 | 牛牛大作栈](http://www.imsry.cn/posts/5c3bf0b6.html) {% asset_link web1.png 備份圖 %}
  有驗證上面兩個函示，得出來結果都是相等的。
  但注意，在上面 echo 出來，網頁或 log 上面都不會有字元
  要看 var_dump 會比較準

* [AES/CBC/PKCS5Padding 的 PHP 实现 - luoxiaojun1992 的个人空间 - OSCHINA](https://my.oschina.net/luoxiaojun1992/blog/883123)

主要的填充算法有填充 NUL("0") 和 PKCS7，Mcrypt 默認使用的 NUL("0") 填充算法，當前已不被推薦，OpenSSL 則默認模式使用 PKCS7 對數據進行填充並對加密後的數據進行了 base64encode 編碼，所以建議開發中使用 PKCS7 對待加密數據進行填充，已保證通用性（alipay sdk 中雖然使用了 Mcrypt 加密簇，但使用 PKCS7 算法對數據進行了填充，這樣在一定程度上親和了 OpenSSL 加密算法）。

原文網址：https://kknews.cc/zh-tw/other/9pgvlpl.html

[php 如何 openssl_encrypt 加密解密 - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000016804661)
有說`OPENSSL_ZERO_PADDING`會自動填充
但測試結果沒有效果 OPENSSL_RAW_DATA 方式【会用 PKCS#7 进行补位】

## 簡單說我對簽章的定義

{% asset_img https://upload.wikimedia.org/wikipedia/commons/thumb/6/66/Digital_Signature_diagram_zh-CN.svg/1280px-Digital_Signature_diagram_zh-CN.svg.png 簽章流程 %}

簽章主要目的加密，而是防止資料有被串改

ebc 是沒有 iv 的，cbc 是有 iv 的
有用 iv 會比較安全，但是 iv 長度是有限制的
[lyhiving/aes: 兼容 php、js、JAVA 的 AES 加密](https://github.com/lyhiving/aes)
詳細的我就沒研究

當天跟所有公司留到半夜 4:30 回去，也許這就是技術債!!!
希望不要有上班回家的時候不要再看到太陽出來了

題外小記
matrix 做翻書效果，搭配 向量矩陣 和[缓动函数速查表](https://www.xuanfengge.com/easeing/easeing/)
有很好展示效果

[jlmakes/rematrix: Matrix transformations made easy.](https://github.com/jlmakes/rematrix)
[The CSS3 Matrix Construction Set](http://www.useragentman.com/matrix/#E4UwziAuBCCumQPYDsBMBeASuKACAYosALYBkAZsIsQAwAe6NFVtAno6UvegMwAcAFk6Ia7AIwA2IZWpjxNJkjEMeAicLnpU25tVQMxC4fvQBWAOymAdAE47NiTYF9UA1GJvHxAnsJ4MfXyQedlVUUgATRB4AEXRIYFgQUgBLCLF0AGVYACNiFMhUiIxsCEKAC0hiABsAYRRIEGRIdAAeAAcAPgAVcpSwXH7cAENcHOrEAGMAa1wQapBiJsgrVoB6LoAoUkmwMHrm5fQ1gCpN3BOCEHncSmvcJDmIgpHkVlxEcgfykFwwSFYCwGOXmiAA7lZzic1ptNqc5sgwLBQCNqtVvr95otlgN-oCQBExqCwbhYBAxkQIiBgABaHKIOgXGFWcZTaYAGlwAGIAObANK4ADe51wovpdBpYBSAC8UsgeQAuCnAKm08UAbk2AF9YfDer9EDkAFYgSaQInkIi-F5DBLDRGWkgEpmbLmIMRCkVi4YzPmIWDICJKrnkciTcOTTU6uGXfUfY2m83DciNYCDc3lYbA67IB7Ae1gR1LQnQ12IVCe0Xe31UANB7kRyahqO6y71YjEFB-AFA25ECmMw0ms1gKwulkTGaVquW5qSmUgJVyn78yBR3Ct3C1TKZPtpyA-XB8gWl3n8wnCqtjH3TP11pXI6oACgARCliMMeeA1jkb3fA1YPIpOQL4AJS4KA7QgMM5pgJMVBorgNBIbgYJ9I0mpXj8KQ8pUSp8Ao7R0JhVbtIgUqQCkKBKqA1QwSkABuIAkaKiBMcA5ATGCSp9BEVLIC2RQ8FkuT5OaAAS3QALIADKvIS26ZEUAjoLU9qTPMRSmOgkwTBARQSDpekgEAA)
[演算法筆記 - Linear Function](http://www.csie.ntnu.edu.tw/~u91029/LinearFunction.html)
