---
title: Input Path Not Canonicalized 風險
date: 2022-07-30 23:55:13
tags: [path]
categories: 源碼掃描
---
常常遇到一些問題，網路上找到一些方法筆記一下。

<!--more-->

```java=
private static String sanitizePathTraversal(String filename) {
 Path p = Paths.get(filename);
 return p.getFileName().toString();
}
private String getFileContents_fixed(HttpServletRequest request) throws ServletException,
FileNotFoundException, IOException {
String filename = sanitizePathTraversal(request.getParameter("filename")); // Ensures access only to files in a given folder, no traversal
Path path = Paths.get(SERVED_FILES_DIR + filename);
byte[] fileContentBytes = Files.readAllBytes(path);
String fileContents = new String(fileContentBytes, FILE_CONTENT_ENCODING_STRING);
 return fileContents;
}

```

簡單說就是讓使用者 request 檔案名稱不可以帶入資料夾相關資料，看上面範例。但不知道Java有沒有現成安全的套件，目前是沒看到有。


## Path Manipulation

自己覺得可以用白名單確認，可能是最好。再來是過濾特殊字元。最後就算沒有改 Code，正常主機設定的權限也應該要嚴謹，也不會有安全性的問題。

### 白名單

參考:[Path Manipulation | Fix Fortify Issue](http://fortifyissue.blogspot.com/2014/08/path-manipulation.html)
```java=
public static final String UMD_EXCEPTION ="/fxxxxbs3/UMD_EXCE/";
 public static final Map<String, String> UMD_EXCEPTION_OMC = Collections.unmodifiableMap(new HashMap<String, String>(){{
  for(String omcCode :OmcUnit.ALL_CODES){
   put(omcCode,UMD_EXCEPTION+File.separator+omcCode); 
  }
 }});


for(OmcUnit omcUnit :omcList){
    String fileName = "omc"+omcUnit.getCode()+"."+inputDateRoc+".all";
    String path = FileFullPathUrl.UMD_EXCEPTION_OMC.get(omcUnit.getCode());
File path = new File(path);
}

```


### 過濾特殊字元
參考
[Fortify漏洞之 Path Manipulation 路徑篡改問題解決筆記_dazhong2012的博客-CSDN博客_pathmanipulation](https://blog.csdn.net/dazhong2012/article/details/88564067)

```java=
/**
 * 過濾字符串幫助類
 */
public class CleanPathUtil {
	public static String cleanString(String aString) {
	    if (aString == null) return null;
	    String cleanString = \"\";
	    for (int i = 0; i < aString.length(); ++i) {
	        cleanString += cleanChar(aString.charAt(i));
	    }
	    return cleanString;
	}

	private static char cleanChar(char aChar) {

	    // 0 - 9
	    for (int i = 48; i < 58; ++i) {
	        if (aChar == i) return (char) i;
	    }

	    // 'A' - 'Z'
	    for (int i = 65; i < 91; ++i) {
	        if (aChar == i) return (char) i;
	    }

	    // 'a' - 'z'
	    for (int i = 97; i < 123; ++i) {
	        if (aChar == i) return (char) i;
	    }

	    // other valid characters
	    switch (aChar) {
	        case '/':
	            return '/';
	        case '.':
	            return '.';
	        case '-':
	            return '-';
	        case '_':
	            return '_';
	        case ' ':
	            return ' ';
	    }
	    return '%';
	}

}

```


可能還有其他做法，改天看到再補。
[FIO16-J. Canonicalize path names before validating them - SEI CERT Oracle Coding Standard for Java - Confluence](https://wiki.sei.cmu.edu/confluence/display/java/FIO16-J.+Canonicalize+path+names+before+validating+them)

## 以下方法為不可用

以下為不可用安全。~~我差點用了。~~

```java=
import java.nio.file.*;

String path  = System.getenv(variableName);
Path p = Paths.get(path);
Path normalizedPath = p.normalize();
path = new File(normalizedPath.toString());
```


```java=
import org.apache.commons.io.FilenameUtils;

String path  = System.getenv(variableName);
path = new File(FilenameUtils.normalize(path));
```

[path - Input_Path_Not_Canonicalized - PathTravesal Vulnerability in checkmarx - Stack Overflow](https://stackoverflow.com/questions/66171030/input-path-not-canonicalized-pathtravesal-vulnerability-in-checkmarx)
[FilenameUtils (Apache Commons IO 2.11.0 API)](https://commons.apache.org/proper/commons-io/apidocs/org/apache/commons/io/FilenameUtils.html#normalize-java.lang.String-)
[Top 20 OWASP Vulnerabilities And How To Fix Them Infographic | UpGuard](https://www.upguard.com/blog/top-20-owasp-vulnerabilities-and-how-to-fix-them)