---
title: Jenkins 執行 Java 程式亂碼解決方法
date: 2020-07-30 20:30:16
tags: [linux, java]
categories: Linux
---

最近公司上板 Java 排程程式都會遇到亂碼 ????
這個都不能手動執行
今天解決這個問題
整理一下

<!--more-->

首先，簡單講一下
我們 jenkins 上板程式是透過 ssh 打指令操作

> 這邊我備註一下，我們公司上板 Java 程式，是排程程式，這些排程不是使用 Crontab 方式去執行
> 所以這邊執行都是透過 shell 腳本方式去重複執行

有在家目錄使用`socure ~/.profile`
但是這個過程
奇怪的是 locale 查看出來的 LC_CTYPE=en_US.UTF8 其他都是 POSIX 
一般用 Xshell 登入是 locale 全看到 en_US.UTF8

這邊我還不確定是什麼原因?
> non-interactive + non-login shell
> 最後一種模式為非交互非登陸的shell，創建這種shell典型有兩種方式：
>    bash script.sh
>     ssh user@remote command
> 這兩種都是創建一個shell，執行完腳本之後便退出，不再需要與用戶交互。

[ssh連接遠程主機執行腳本的環境變量問題_whitehack的專欄-CSDN博客_ssh 遠程執行命令 含變量](https://blog.csdn.net/whitehack/article/details/51705889) {% asset_link web1.png 備份圖 %}
目前看到這篇有疑似這個問題
所以導致登入環境變數會有不一樣結果
我也沒看到有設定 BASH_ENV 參數

不過確實因為 ssh remote  command 登入
造就登入不一樣結果

裡面有滿清楚內容[ssh連接遠程主機執行腳本的環境變量問題_whitehack的專欄-CSDN博客_ssh 遠程執行命令 含變量](https://blog.csdn.net/whitehack/article/details/51705889)、[關於 Linux 下 Bash 與 Zsh 啟動檔的載入順序研究 | The Will Will Web](https://blog.miniasp.com/post/2021/07/26/Bash-and-Zsh-Initialization-Files)
> ```
+----------------+--------+-----------+---------------+
|                | login  |interactive|non-interactive|
|                |        |non-login  |non-login      |
+----------------+--------+-----------+---------------+
|/etc/profile    |   A    |           |               |
+----------------+--------+-----------+---------------+
|/etc/bash.bashrc|        |    A      |               |
+----------------+--------+-----------+---------------+
|~/.bashrc       |        |    B      |               |
+----------------+--------+-----------+---------------+
|~/.bash_profile |   B1   |           |               |
+----------------+--------+-----------+---------------+
|~/.bash_login   |   B2   |           |               |
+----------------+--------+-----------+---------------+
|~/.profile      |   B3   |           |               |
+----------------+--------+-----------+---------------+
|BASH_ENV        |        |           |       A       |
+----------------+--------+-----------+---------------+
```
>
> 配置文件建議
> 
> 回顧一下前面提到的所有配置文件，總共有以下幾種：
> 
>    /etc/profile
>    ~/.bash_profile
>    ~/.bash_login
>    ~/.profile
>    /etc/bash.bashrc
>    ~/.bashrc
>    $BASH_ENV
>    $ENV
>
> 不知你是否會有疑問，這麼多的配置文件，究竟每個文件裡面應該包含哪些配置，比如PATH應該在哪？提示符應該在哪配置？啟動的程序應該在哪？等等。所以在文章的最後，我蒐羅了一些最佳實踐供各位參考。（這裡只討論屬於用戶個人的配置文件）
>
>    ~/.bash_profile：應該儘可能的簡單，通常會在最後加載.profile和.bashrc(注意順序)
>    ~/.bash_login：在前面討論過，別用它
>    ~/.profile：此文件用於login shell，所有你想在整個用戶會話期間都有效的內容都應該放置於此，比如啟動進程，環境變量等
>    ~/.bashrc：只放置與bash有關的命令，所有與交互有關的命令都應該出現在此，比如bash的補全、alias、顏色、提示符等等。特別注意：別在這裡輸出任何內容（我們前面只是為了演示，別學我哈）

因為我猜測 jenkins 進去跟我用 xshell 語系不一樣
所以就做了 log 紀錄，發現真的不一樣
```bash
export LC_ALL="en_US.UTF-8"
date >> /tmp/test_ssh.log
env >> /tmp/test_ssh.log
echo "--------" >> /tmp/test_ssh.log
locale >> /tmp/test_ssh.log
echo "=======" >> /tmp/test_ssh.log
```

但不是上面問題

之前看到使用 file.enconding 去解決
~~但實際用上卻沒有效果~~
跟 file.enconding 沒關係，是我使用 JAVA_OPTS環境變數
沒有效果，後面會講到

> java 預設在處理文字檔案的寫入時，
> 會以 os 的預設編碼當作寫入的編碼規則，
> 這個問題常會發生在 windows 英文的作業系統要寫入中文的時候發生，
> 
> 解法1：
> 如果是用 java 執行的 jar ，
> java -Dfile.encoding=UTF8 -jar e:\bin\ExportToFile.jar
> 
> 解法2：
> 加入環境變數 JAVA_OPTS，
>   windows 環境：
> JAVA_OPTS=-Dfile.encoding=UTF8;%JAVA_OPTS%
>   linux 環境：
> $> export JAVA_OPTS=-Dfile.encoding=UTF8:$JAVA_OPTS
> 
> 解法3：
> 在程式裡面設定：
> 取得目前系統檔案編碼屬性 String defaultEncodingName = System.getProperty( "file.encoding" );
> 設定系統檔案編碼屬性 System.setProperty("file.encoding", "UTF-8");
[解決 java 寫檔的中文亂碼問題 @ caffeine :: 隨意窩 Xuite日誌](https://blog.xuite.net/akuox/caffeine/32265749-%E8%A7%A3%E6%B1%BA+java+%E5%AF%AB%E6%AA%94%E7%9A%84%E4%B8%AD%E6%96%87%E4%BA%82%E7%A2%BC%E5%95%8F%E9%A1%8C)



## 了解一下 LC_* 環境變數

### wiki 

> 在Ubuntu作業系統中，使用man locale-gen或man locale可獲得關於locale實現的細節。實際上是由glibc庫實現的。
> 
> locale相關（環境）變數生效的優先順序：[1]
> 
>     LANGUAGE 指定個人對語言環境值的主次偏好，例如zh_CN:en_US:en
>     LC_ALL 這不是一個環境變數，是一個可被C語言庫函式setlocale設定的宏，其值可覆蓋所有其他的locale設定。因此> 預設時此值為空
>     LC_xxx 可設定locale各方面（category）的值，可以覆蓋LANG的值。
>     LANG 指定預設使用的locale值
> 
> 可以把上述環境變數設在/etc/profile 或 /etc/environment等系統初始檔案中。值得注意的是，若LANG或LC_ALL被設> 定為 "C"，那麼LANGUAGE的值將被忽視。 [2]
> 
> 除 C 和 POSIX這兩個locale名稱外，locale的名稱並未標準化。Linux平台與Windows系統的locale名稱有很大不同。> Linux名稱的命名規則為：
> 
>  language[_territory[.codeset]][@modifier]
> 
> 其中language是ISO 639-1標準中定義的雙字母的語言代碼

### 暫時語系用法

感覺是暫時環境變數，但沒有詳細看到有人這樣介紹

> $ LC_TIME=en_US.UTF-8 date
> Fri Oct 31 19:51:16 CST 2014
> $ LC_TIME=fi_FI.UTF-8 date
> pe 31.10.2014 19.52.00 +0800
> $ LC_TIME=zh_CN.UTF-8 date
> 2014年 10月 31日 星期五 19:53:07 CST

PS: 上面使用 locale -a 有安裝成功的才能用

### POSIX 和 C

[更改 Linux 系統語言環境變量 - Jamin Zhang](https://jaminzhang.github.io/linux/Change-Linux-System-Locale-Envs/)

> LC_ALL=C 是為了去除所有本地化的設置，讓命令能正確執行。
> C 是系統默認的 locale，」POSIX」是」C」的別名。所以當我們新安裝完一個系統時，默認的 locale 就是 C 或 POSIX。

### to default locale: No such file or directory

[Locale - Ubuntu 18.04 探索筆記](https://samwhelp.github.io/note-ubuntu-18.04/read/howto/install/locale/)

會多出現三行

ssh remote bash 登入使用 locale 指令會出現 

> locale: Cannot set LC_CTYPE to default locale: No such file or directory
> locale: Cannot set LC_MESSAGES to default locale: No such file or directory
> locale: Cannot set LC_ALL to default locale: No such file or directory
> 
> 只要執行下面的指令，就可以解決
> 
> $ sudo locale-gen zh_TW.UTF-8

但我沒使用這個方案解決

至於原因可能是
[Linux Xshell登录报错 locale: Cannot set LC_CTYPE to default locale: No such file or directory（已解决）_醉世老翁的博客-CSDN博客_locale -a locale: cannot set lc_ctype to default l](https://blog.csdn.net/wojiuwangla/article/details/107556417)
[CentOS6.5手动安装glibc-2.14后locale出现No such file or directory终极解决办法_guitar___的博客-CSDN博客_centos character map file `utf-8 not found: no su](https://blog.csdn.net/guitar___/article/details/77651983)

## 查看 Java file encoding

[Java May Use UTF-8 as its Default Charset - DZone Java](https://dzone.com/articles/java-may-use-utf-8-as-its-default-charset)

CharsetDemo.java
```java CharsetDemo.java
public class CharsetDemo
{
   /**
    * Supplies the default encoding without using Charset.defaultCharset()
    * and without accessing System.getProperty("file.encoding").
    *
    * @return Default encoding (default charset).
    */
   public static String getEncoding()
   {
      final byte [] bytes = {'D'};
      final InputStream inputStream = new ByteArrayInputStream(bytes);
      final InputStreamReader reader = new InputStreamReader(inputStream);
      final String encoding = reader.getEncoding();
      return encoding;
   }

   public static void main(final String[] arguments)
   {
      out.println("Default Locale:   " + Locale.getDefault());
      out.println("Default Charset:  " + Charset.defaultCharset());
      out.println("file.encoding:    " + System.getProperty("file.encoding"));
      out.println("sun.jnu.encoding: " + System.getProperty("sun.jnu.encoding"));
      out.println("Default Encoding: " + getEncoding());
   }
}
```

[環境變量JAVA_TOOL_OPTIONS、_JAVA_OPTIONS、JAVA_OPTS設置_ly199108171231的博客-CSDN博客_java_options](https://blog.csdn.net/ly199108171231/article/details/85253946) {% asset_link web2.png 備份圖 %}
這邊查到 JAVA_OPTS 是 Tomcat 用的
難怪我試個老半天都沒有什麼效果
JAVA_TOOL_OPTIONS 確實就正常了

>    JAVA_OPTS：常用於一些應用的配置，如Tomcat，但它一般不作為環境變量，也不能被JVM識別的，是那些應用的自定義配置；
>    _JAVA_OPTIONS：也是作為環境變量來替代命令行參數的，但它是JVM廠家自定義的，可以覆蓋JAVA_TOOL_OPTIONS，但各廠家的不同，_JAVA_OPTIONS是Oracle的JVM，而IBM的則是用IBM_JAVA_OPTIONS。
>     JAVA_TOOL_OPTIONS：是標準的，所有虛擬機都能識別和應用的。
> 
> 如果想驗證上面的不同也不難，如果設置了JVM能識別的環境變量，JVM會有"Picked up"的提示的，如：
> 
>     export JAVA_OPTS=zhaiqiafneng
>     export JAVA_TOOL_OPTIONS="-Xmx512m -Xms64m" 
>      
>     java -version 
> 
> JVM會打印：
> 
>     Picked up JAVA_TOOL_OPTIONS: -Xmx512m -Xms64m
>     java version "1.8.0_101"
>     Java(TM) SE Runtime Environment (build 1.8.0_101-b13)
>     Java HotSpot(TM) 64-Bit Server VM (build 25.101-b13, mixed mode)


```bash
# Big5
LC_ALL=zh_TW.BIG5  java Xxxx
# Big5
java -Dfile.encoding=big5 Xxxx
# Big5
LC_ALL=zh_TW.UTF8  java -Dfile.encoding=big5 Xxxx
```

排程踩雷注意
[linux 中crontab 定时 shell脚本启动jar包，tomcat失败_醉世老翁的博客-CSDN博客_linux 定时任务启动的tomcat jar包不一样](https://blog.csdn.net/wojiuwangla/article/details/107365921)

### Java  properties and XML and log4j

固定都吃 ISO-8859-1
.properties檔案的編碼是ISO-8859-1，又稱為Latin-1。所有非Latin-1字元必須利用Unicode跳脫字元錄入，例如\uHHHH中，HHHH是某個字元的Unicode字元集的十六進位的索引。這樣就可以使用.properties檔案作為在地化的屬性資源包。非Latin-1的文字檔案，可以通過使用隨JDK提供的native2ascii工具轉換為正確的.properties檔案；或使用如po2prop[1]的第三方工具，管理雙語在地化格式跳脫為.properties

難怪... .properties 無法正常看到 UTF-8

[.properties - 維基百科，自由的百科全書](https://zh.wikipedia.org/zh-tw/.properties)

XML 是吃 UTF-8

原本想獨立寫一篇測試
但內容不太多，所以就整理在這邊

log4j 也能設定指定 encoding，不過...這邊跟 Java 一樣，預設是帶 System 帶入的
[log4j日誌記錄到文件 - Log4j教學](http://tw.gitbook.net/log4j/log4j_logging_files.html)

## 相關網站整理

裡面也混雜一些彩蛋

* [區域化環境類別 (categories)](https://www.csie.ntu.edu.tw/~r95053/samples/collection/backup/locale2.html)
* [地區標籤](https://openhome.cc/Gossip/Encoding/LocaleTag.html)
* [java.util.Locale.getDefault()方法實例 - Java.util包](http://tw.gitbook.net/java/util/locale_getdefault.html)
* [locale的设定及LANG、LC_CTYPE、LC_ALL环境变量 - Lo0ong - 博客园](https://www.cnblogs.com/xlmeng1988/archive/2013/01/16/locale.html)
* [解決 macOS SSH 連線中文亂碼的問題 - Kaix Blog](https://kaix.dev/2018/06/30/macos-ssh-chinese/)
* [jvm 性能调优工具之 jinfo - 简书](https://www.jianshu.com/p/8d8aef212b25)
* [locale错误导致Java中文乱码错误的总结_clamaa的专栏-CSDN博客_java locale 中文乱码](https://blog.csdn.net/clamaa/article/details/70046217)
* [詳解Linux中文亂碼問題終極解決方法 | 程式前沿](https://codertw.com/%E4%BC%BA%E6%9C%8D%E5%99%A8/380106/)
* [locale的设定及LANG、LC_CTYPE、LC_ALL环境变量_laven90的专栏-CSDN博客_如何设置 locale](https://blog.csdn.net/laven90/article/details/9285307)
* [解決 java 寫檔的中文亂碼問題 @ caffeine :: 隨意窩 Xuite日誌](https://blog.xuite.net/akuox/caffeine/32265749-%E8%A7%A3%E6%B1%BA+java+%E5%AF%AB%E6%AA%94%E7%9A%84%E4%B8%AD%E6%96%87%E4%BA%82%E7%A2%BC%E5%95%8F%E9%A1%8C)
* [Linux下配置Jenkins+gitlab持續整合構建流程 | IT人](https://iter01.com/64096.html)
* [Jenkins使用/etc/profile中环境变量异常_jiangshanwe-CSDN博客_jenkins /etc/profile](https://blog.csdn.net/jiangshanwe/article/details/85785226)
* [Jenkins / Hudson environment variables - Stack Overflow](https://stackoverflow.com/questions/5818403/jenkins-hudson-environment-variables)
* [Linux 用 ps 與 top 指令找出最耗費 CPU 與記憶體資源的程式 - G. T. Wang](https://blog.gtwang.org/linux/ps-top-find-processes-by-cpu-memory-usage/)
* [詳解Linux中文亂碼問題終極解決方法 | 程式前沿](https://codertw.com/%E4%BC%BA%E6%9C%8D%E5%99%A8/380106/)
* [更改 Linux 系统语言环境变量 - Jamin Zhang](https://jaminzhang.github.io/linux/Change-Linux-System-Locale-Envs/)
* [locale错误导致Java中文乱码错误的总结_clamaa的专栏-CSDN博客_java locale 中文乱码](https://blog.csdn.net/clamaa/article/details/70046217)
* [bash 四种工作模式及其相关的配置文件](http://hazirguo.github.io/articles/2017/bash_login_interactive_mode.html)
* [Linux系统bash的四种模式与jenkins执行shell的环境变量问题_Robin Hu的专栏-CSDN博客_jenkins shell环境变量](https://blog.csdn.net/hudashi/article/details/82464995)
* [ssh連接遠程主機執行腳本的環境變量問題_whitehack的專欄-CSDN博客_ssh 遠程執行命令 含變量](https://blog.csdn.net/whitehack/article/details/51705889)
* [SDKMAN is able to install on Java on ARM · Issue #640 · sdkman/sdkman-cli](https://github.com/sdkman/sdkman-cli/issues/640)

### 其他連結(很多重複)

* [FileWriter (Java SE 12 )](https://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/io/FileWriter.html)
* [Java丨Cmd運行Jar出現亂碼問題 - 台部落](https://www.twblogs.net/a/5b8812fc2b71775d1cda5350)
* [Linux中java log输出中文乱码 中文变“?”_一起进步的博客-CSDN博客_linux log日志中文乱码](https://blog.csdn.net/qq_27292113/article/details/71404298)
* [linux中使用tail -f查看日志出现中文乱码的解决方案_付之一笑的专栏-CSDN博客_linux 日志乱码](https://blog.csdn.net/btt2013/article/details/54616793?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-9.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-9.channel_param)
* [log4j日志文件乱码解决方法_log4j中文乱码,log4j中文乱码-Java文档类资源-CSDN下载](https://download.csdn.net/download/tw121256081/9427273)
* [Linux下中文变问号的解决办法_sui102的专栏-CSDN博客_liunx windows 中文字符变问号](https://blog.csdn.net/sui102/article/details/84495497)
* [log4j乱码问题解决办法_linux上配置log4j,linux配置log4j-Java文档类资源-CSDN下载](https://download.csdn.net/download/adnaper/4710011)
* [Java 程式處理網址 URL 百分比編碼與解碼教學 - G. T. Wang](https://blog.gtwang.org/programming/url-percent-encoding-and-decoding-using-java/)
* [Java讀帶有BOM的UTF-8檔案亂碼 - IT閱讀](https://www.itread01.com/p/848586.html)
* [Java -Dfile.encoding=UTF-8的使用_haixia_12的专栏-CSDN博客_-dfile.encoding=utf-8](https://blog.csdn.net/haixia_12/article/details/42424513)
* [log4j 控制台和文件输出乱码问题解决](http://www.coderli.com/log4j-console-file-garbled/)
* [[轉]log4j日誌檔案亂碼問題的解決方法 - IT閱讀](https://www.itread01.com/p/570130.html)
* [utf 8 - Setting the default Java character encoding - Stack Overflow](https://stackoverflow.com/questions/361975/setting-the-default-java-character-encoding)
* [file - JVM property -Dfile.encoding=UTF8 or UTF-8? - Stack Overflow](https://stackoverflow.com/questions/6031877/jvm-property-dfile-encoding-utf8-or-utf-8)
* [理解環境變數 JAVA_TOOL_OPTIONS | 程式前沿](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/99795/)
* [牙根 會痛 假牙 - Google 搜尋](https://www.google.com/search?client=firefox-b-d&q=%E7%89%99%E6%A0%B9+%E6%9C%83%E7%97%9B+%E5%81%87%E7%89%99)
* [shell ssh command - Google 搜尋](https://www.google.com/search?q=shell+ssh+command&client=firefox-b-d&sxsrf=ALeKk01FDso58ld-3nJS1BtA0NDR-ZEJ8Q:1595611007298&ei=fxcbX9jZEY2Ur7wPlcuRmAc&start=20&sa=N&ved=2ahUKEwiYlaiasubqAhUNyosBHZVlBHM4ChDw0wN6BAgOEEM&biw=1280&bih=644)
* [A few ways to execute commands remotely using SSH · zaiste.net](https://zaiste.net/posts/few-ways-to-execute-commands-remotely-ssh/)
* [ssh 遠端 執行程式 log 亂碼 lang - Google 搜尋](https://www.google.com/search?client=firefox-b-d&biw=1280&bih=644&sxsrf=ALeKk016alYo2SrL6mJ7XnrDFEqsqx4--w%3A1595611426430&ei=IhkbX8zrGf-Ur7wPiIq5gAo&q=ssh+%E9%81%A0%E7%AB%AF+%E5%9F%B7%E8%A1%8C%E7%A8%8B%E5%BC%8F+log+%E4%BA%82%E7%A2%BC+lang&oq=ssh+%E9%81%A0%E7%AB%AF+%E5%9F%B7%E8%A1%8C%E7%A8%8B%E5%BC%8F+log+%E4%BA%82%E7%A2%BC+lang&gs_lcp=CgZwc3ktYWIQAzIFCAAQzQI6BwgjELADECc6BAgjECdQ3D9YsEtgxExoAnAAeACAAU2IAacDkgEBN5gBAKABAaoBB2d3cy13aXrAAQE&sclient=psy-ab&ved=0ahUKEwjMhJbis-bqAhV_yosBHQhFDqA4ChDh1QMICw&uact=5)
* [SSH訪問linux 亂碼問題 - IT閱讀](https://www.itread01.com/content/1496132534.html)
* [清水國中資訊組 | locale語系設定UTF-8與Big5](http://www.csjs.tc.edu.tw/lt/index.php?op=ViewArticle&articleId=528&blogId=5)
* [解決遠端 SSH 連線主機時文字編碼設定 locale 錯亂問題 | 一介資男](https://www.mxp.tw/5421/)
* [[ssh] — 如何從〜/ .ssh / config禁用ssh_config中設置的SendEnv變量](https://zh.it-reply.net/q/%E5%A6%82%E4%BD%95%E5%BE%9E-ssh-config%E7%A6%81%E7%94%A8sshconfig%E4%B8%AD%E8%A8%AD%E7%BD%AE%E7%9A%84sendenv%E8%AE%8A%E9%87%8F-4795)
* [關閉 SSH SendENV 解決 locale 問題 | Somewhere I Belong](https://yulun.me/2016/ssh-remote-sendenv-locale-issue/)
* [增進 SSH 使用效率 - ssh_config · 完全用 GNU/Linux 工作](https://chusiang.gitbooks.io/working-on-gnu-linux/content/20.ssh_config.html)
* [SendEnv jenkins ssh 亂碼 - Google 搜尋](https://www.google.com/search?q=SendEnv++jenkins+ssh+%E4%BA%82%E7%A2%BC&client=firefox-b-d&sxsrf=ALeKk02NHeZqOFBICu1lc9oM33eKuEKtLg:1595612479397&ei=Px0bX_vfF4eTr7wPhZq5gAI&start=10&sa=N&ved=2ahUKEwi7-6HYt-bqAhWHyYsBHQVNDiAQ8NMDegQIDBBD&biw=1280&bih=620)
* [jenkins 节点主机在不同系统导致乱码的解决办法 - 花花兽的个人页面 - OSCHINA](https://my.oschina.net/sunlimiter/blog/877025)
* [Jenkins on Windows 心得分享 (03)：有效避免記錄檔或訊息出現亂碼的方法 | The Will Will Web](https://blog.miniasp.com/post/2015/12/24/Jenkins-on-Windows-03-Avoid-messy-words-in-log-messages)
* [Jenkins之解决乱码问题_ZZY1078689276的专栏-CSDN博客_jenkins utf-8 文件乱码](https://blog.csdn.net/ZZY1078689276/article/details/77839045)
* [【转】ssh 中文乱码问题_安科网](https://www.ancii.com/asceau5ym/)
