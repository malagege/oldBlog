---
title: 'JavaScript,Java 實作 AES/CBC/NoPadding 加密方法'
date: 2021-08-22 15:41:55
tags: [aes,cbc,nopadding,java,javascript]
categories: 加密
---
最近公司 API 原有測試程式需要改前後端，才能測試 API。最近在想能不能搭配 Vite & Vue 能實現前端測試 API。API有做加密，今天就先研究如何實現 AES/CBC/NoPadding加密。

<!--more-->

## 公司使用情境

API 接口所有參數會經過第一層加密(包最外層變數)，這邊會有一個外部key，內層資料會用內部key再做一次加密。


這邊我還是小小抱怨一下，公司這篇稱外部key為私鑰，內部key為公鑰，我這邊原本看到這邊應該是非對稱加密，當初裡面長輩也說這邊是非對稱加密，但我最近找了一下套件發現不是，之前一直在想這邊怎麼產生公鑰(內部key)、私鑰(外部key)，這邊可以不用想了，密碼應該也是rand 產生出來了。根本不是非對稱加密。

這邊不是重點，我們趕快來想想怎麼比較快錯出來。

## 解決方案

我想快速做出來，這邊有想想幾個方案

內部程式

- node-java: 
載入 Java Method 呼叫加密程式。但我沒有採用此方案，因為還要而外裝node-gpy ，可能還要搭配 node-electron 來使用，修改程式便利性又降低了。
- vite-electron-plugin
本來想採取上面方案搭配，但是原因同上。而且風險係數太多。
- crypto 
好像也能做到，但好像看到相關文章寫沒支援 Padding設定?? 所以先跳過
- crypto-js 
可以用在web上面，後來採用這個方案。

## 實作 Java/JavaScript 做 AES/CBC/NoPadding

網路上這個範例可說是零，我自己整理出來趕快記筆記。

### Java

```java=
    public static String decryptBase64StrByAES(String encodeBase64Str, String decrypt,String iv) 
            throws UnsupportedEncodingException,InvalidAlgorithmParameterException,NoSuchPaddingException,NoSuchAlgorithmException,InvalidKeyException,BadPaddingException,IllegalBlockSizeException {

            byte[] unDecryptByte = Base64.decodeBase64(encodeBase64Str);
            Cipher deCipher = Cipher.getInstance("AES/CBC/NoPadding");

            SecretKeySpec decryptSpec = new SecretKeySpec(decrypt.getBytes(), "AES");
            IvParameterSpec ivspec = new IvParameterSpec(iv.getBytes());
            deCipher.init(Cipher.DECRYPT_MODE, decryptSpec, ivspec);
            
            return new String(deCipher.doFinal(unDecryptByte), "UTF-8").trim();
        }

    public static String encryptBase64StrByAES(String unDecryptStr, String decrypt, String iv) 
            throws UnsupportedEncodingException,InvalidAlgorithmParameterException,NoSuchPaddingException,NoSuchAlgorithmException,InvalidKeyException,BadPaddingException,IllegalBlockSizeException {
        
            Cipher cipher = Cipher.getInstance("AES/CBC/NoPadding");

            // 補齊16倍數，不足padding zero
            int blockSize = cipher.getBlockSize();
            
            byte[] dataBytes = unDecryptStr.getBytes("UTF-8");
            int plaintextLength = dataBytes.length;
            if (plaintextLength % blockSize != 0) {
                plaintextLength = plaintextLength + (blockSize - (plaintextLength % blockSize));
            }
            byte[] plaintext = new byte[plaintextLength];
            System.arraycopy(dataBytes, 0, plaintext, 0, dataBytes.length);

            SecretKeySpec decryptSpec = new SecretKeySpec(decrypt.getBytes(), "AES");
            IvParameterSpec ivspec = new IvParameterSpec(iv.getBytes());
            cipher.init(Cipher.ENCRYPT_MODE, decryptSpec, ivspec);
            
            return Base64.encodeBase64String(cipher.doFinal(plaintext));
        }
```

或

```java=
/*
 * 
 */
package com.serpro.library.String;

import java.security.NoSuchAlgorithmException;
import org.apache.commons.codec.binary.Base64;

import javax.crypto.Cipher;
import javax.crypto.NoSuchPaddingException;
import javax.crypto.spec.IvParameterSpec;
import javax.crypto.spec.SecretKeySpec;

public class MCrypt {

        private String iv;
        private String SecretKey;
        
        private IvParameterSpec ivspec;
        private SecretKeySpec keyspec;
        private Cipher cipher;


        public MCrypt(String iv, String key)
        {
            try{

                this.setIv(iv);
                this.setSecretKey(key);

            } catch (Exception e) {

            }

            ivspec = new IvParameterSpec(iv.getBytes());

            keyspec = new SecretKeySpec(SecretKey.getBytes(), "AES");

            try {
                    cipher = Cipher.getInstance("AES/CBC/NoPadding");
            } catch (NoSuchAlgorithmException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
            } catch (NoSuchPaddingException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
            }
        }

        public String getIv(){
            return this.iv;
        }

        public IvParameterSpec getIvSpc(){
            return this.ivspec;
        }

        private void setIv(String iv) throws Exception {
            if(iv.length() != 16){
                throw new Exception("Quantidade IV inválida");
            }
            this.iv = iv;
        }

        private void setSecretKey(String key) throws Exception{
            if(key.length() != 16){
                throw new Exception("Quantidade Key Inválido");
            }
            this.SecretKey = key;
        }

        public static void main(String[] args) {
            
            try{

                MCrypt mcrypt = new MCrypt("9sb9510e375d44f0", "9756e93d09054s4f");  

                String encrypted = mcrypt.encrypt("佩德羅加布里埃爾 佩德羅加布里埃爾"); 
                String decrypted = mcrypt.decrypt(encrypted);

                System.out.println(encrypted);
                System.out.println(decrypted);

            }  catch(Exception e){

                System.out.println(e.getMessage());

            }
        }

        public String encrypt(String text) throws Exception
        {
            if(text == null || text.length() == 0)
                    throw new Exception("Empty string");

            byte[] encrypted = null;

            try {
                cipher.init(Cipher.ENCRYPT_MODE, keyspec, ivspec);

                encrypted = cipher.doFinal(padString(text).getBytes());
            } catch (Exception e)
            {                       
                throw new Exception("[encrypt] " + e.getMessage());
            }

            return this.toBase64(encrypted);
        }

        public String decrypt(String code) throws Exception
        {
            if(code == null || code.length() == 0)
                    throw new Exception("Empty string");

            byte[] decrypted = null;

            try {
                    cipher.init(Cipher.DECRYPT_MODE, keyspec, ivspec);

                    decrypted = cipher.doFinal(Base64.decodeBase64(code));
                    //Remove trailing zeroes
                    if( decrypted.length > 0)
                    {
                        int trim = 0;
                        for( int i = decrypted.length - 1; i >= 0; i-- ) if( decrypted[i] == 0 ) trim++;

                        if( trim > 0 )
                        {
                            byte[] newArray = new byte[decrypted.length - trim];
                            System.arraycopy(decrypted, 0, newArray, 0, decrypted.length - trim);
                            decrypted = newArray;
                        }
                    }
            } catch (Exception e)
            {
                throw new Exception("[decrypt] " + e.getMessage());
            }

            return new String(decrypted);
        }      

        private static String padString(String source)
        {
            char paddingChar = 0;
            int size = 16;
            int x = source.length() % size;
            int padLength = size - x;

            for (int i = 0; i < padLength; i++)
            {
                  source += paddingChar;
            }

            return source;
        }

        public String toBase64(byte[] buffer){
            return Base64.encodeBase64String(buffer);
        }
}

```

比較要注意 NoPadding 好像要處理文字 Padding，但我看其他PKCS，Java那一段好像都不需要做??



參考:
[Java 使用Cipher类实现加密 - 知乎](https://zhuanlan.zhihu.com/p/349116118)
[Jesse's blog: [Java] 使用Java實做，AES encrypt and decrypt](http://smlie-blog.blogspot.com/2013/07/java-javaaes-encrypt-and-decrypt-example.html)
[java-php-mcrypt-base64/MCrypt.java at master · PgPedroGabriel/java-php-mcrypt-base64](https://github.com/PgPedroGabriel/java-php-mcrypt-base64/blob/master/Java/src/com/serpro/library/String/MCrypt.java)
[java版本的AES/CBC/NoPadding和Python3版本的AES/CBC/NoPadding 记录 - 簡書](https://www.jianshu.com/p/b277b75013e4)
### JavaScript 

```javascript=
import * as CryptoJS from "crypto-js";
function encryptBase64StrByAES(data,key){
    let cipher = CryptoJS.AES.encrypt(data, CryptoJS.enc.Utf8.parse(key), {
        iv: CryptoJS.enc.Utf8.parse(key),
        mode: CryptoJS.mode.CBC,
        padding: CryptoJS.pad.ZeroPadding
    });
    return cipher.toString();
}

function decryptBase64StrByAES(data, key){
    // data = CryptoJS.enc.Base64.parse(data)
    // console.log('dd',data)
    let cipher = CryptoJS.AES.decrypt(data, CryptoJS.enc.Utf8.parse(key), {
        iv: CryptoJS.enc.Utf8.parse(key),
        mode: CryptoJS.mode.CBC,
        padding: CryptoJS.pad.ZeroPadding
    });

    return cipher.toString(CryptoJS.enc.Utf8)
}
```


## 要了解的知識

PRIVATEKEY:必須至少16個字元，最大32個字元，因為使用AES演算法，可以的話，最好給32個字比較安全

IV:必須至少16個字元，最大32個字元，因為使用AES演算法，可以的話，最好給32個字比較安全

HEX: [Hex和Base64 - 疯狂的妞妞 - 博客园](https://www.cnblogs.com/chenss15060100790/p/9079686.html)

URLBASE64
> 標準的Base64並不適合直接放在URL裡傳輸，因為URL編碼器會把標準Base64中的/和+字元變為形如%XX的形式，而這些%號在存入資料庫時還需要再進行轉換，因為ANSI SQL中已將%號用作萬用字元。
>為解決此問題，可採用一種用於URL的改進Base64編碼，它不在末尾填充=號，並將標準Base64中的+和/分別改成了-和_，這樣就免去了在URL編解碼和資料庫儲存時所要做的轉換，避免了編碼資訊長度在此過程中的增加，並統一了資料庫、表單等處物件識別碼的格式。
> 另有一種用於正規表示式的改進Base64變種，它將+和/改成了!和-，因為+，*以及前面在IRCu中用到的[和]在正規表示式中都可能具有特殊含義。 

參考: [Base64 - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/Base64#URL)

## 可以應用地方

token : 但這樣會不會有AES加密後長度會不一致??要怎麼用比較安全，有空來研究。
看起來有各式各樣方式。
目前比較正規是拿 sha256(userid+user_data...+sign_datetime) 產生一組 token ，sign_datetime需要儲存到db，這一組也不需要存到資料庫，到時候token 驗證用db sign_datetime 再做一次 sha256 encode方式測試是否有效，但這是一種 token 只能有一組方法。

另一種做法產生 UUID 然後存放到資料庫裡面，這也是以前比較常見方法。 

JWT 也是最近最常用方式，userid放在jwt裡面，但是重要資訊不能放在上面，不知道這長度會不會一致，有時間再研究。

HTTP Header 不能超過 8kb ，這一點也要注意。

- [PHP 和 Web 端对称加密传输|JSEncrypt|CryptoJS | Laravel China 社区](https://learnku.com/articles/8584/php-and-web-end-symmetric-encryption-transmission-jsencryptcryptojs)
- [简单理解token机制 | 人人都是产品经理](http://www.woshipm.com/pd/877760.html)
- [認識Cookie、Session、Token與JWT | Yang Yang](https://blog.yyisyou.tw/5d272c64/)
- [生成Token - 音视频通信 - 阿里云](https://help.aliyun.com/document_detail/159037.html)
- [传统token与Jwt区别及优缺点_qq_41369135的博客-CSDN博客_jwttoken优点与缺点](https://blog.csdn.net/qq_41369135/article/details/116530607)
- [一次搞懂密碼學中的三兄弟 — Encode、Encrypt 跟 Hash | by Larry Lu | Starbugs Weekly 星巴哥技術專欄 | Medium](https://medium.com/starbugs/what-are-encoding-encrypt-and-hashing-4b03d40e7b0c)
- [JSON Web Token - 在Web应用间安全地传递信息 - 回田园](http://blog.leapoahead.com/2015/09/06/understanding-jwt/)
- [关于token验证几点不明白的地方-CSDN社区](https://bbs.csdn.net/topics/392509818)
- [理解 Keystone 的四种 Token](http://wsfdl.com/openstack/2015/12/26/%E7%90%86%E8%A7%A3Keystone%E7%9A%84%E5%9B%9B%E7%A7%8DToken.html)
- [Alvin Zhu - 关于 Token，你应该知道的十件事](https://alvinzhu.me/web/2014/04/26/10-things-you-should-know-about-tokens.html)
- [Keystone Fernet Token解析 | 开心鬼](https://hackerain.me/2020/11/09/openstack/keystone-fernet-token.html)
- [深入探討 JSON Web Token (JWT) - 墨冊](https://kylinyu.win/jwt-advanced/)


驗證信: 這樣應該就不用存 DB了，時間也可以存裡面，這樣也可以不用讀DB。
網路上大多數方法都是存在 DB，但我想用AES加密應該可行的，後來有找到有人實作。
[java实现绑定邮箱与验证系统 | jay's Blog](https://blog.safeandsound.cn/post/UseJava2ImplimentTheEmail-bindingSystem.html)