---
title: MultipartFile.transferTo 遇到 FileNotFoundException 問題
date: 2022-07-30 23:51:36
tags: [springboot,java]
categories: Java
---


之前公司同事有教，在 Window 在專案 D 朝設定 `/app/file`(參照位置)，執行時候就會吃`D:/app/file`。這個非常實用，不需要再重新配置。

但在用 Spring Boot 會發生讀不到檔案。

```java=
    @Override
    public String store(MultipartFile file, String fileName) throws IOException {

        String destPath "/app/file/";
        File filePath = new File(destPath);
        File dest = new File(filePath, fileName);
        if (!filePath.exists()) {
            filePath.mkdirs();
        }
        try {
            file.transferTo(dest);
            log.info("file save success");
        } catch (IOException e) {
            log.error("File upload Error: ", e);
            throw e;
        }
        return dest.getCanonicalPath();
    }
```



<!--more-->

## 解法

```java=
    @Override
    public String store(MultipartFile file, String fileName) throws IOException {

        String destPath "/app/file/";
        File filePath = new File(destPath);
        
        // Convert to absolute path
        File dest = new File(filePath.getAbsolutePath(), fileName);
        if (!filePath.exists()) {
            filePath.mkdirs();
        }
        try {
            file.transferTo(dest);
            log.info("file save success");
        } catch (IOException e) {
            log.error("File upload Error: ", e);
            throw e;
        }
        return dest.getCanonicalPath();
    }
```
重點是在`File dest = new File(filePath.getAbsolutePath(), fileName);`。


也可以`file.transferTo(filePath.getAbsoluteFile());`一行解決，不過寫完整一點會比較好。


參考來源:
- [[Solved] Spring upload file Error: Multipartfile Transferto() reported an error FileNotFoundException | ProgrammerAH](https://programmerah.com/solved-spring-upload-file-error-multipartfile-transferto-reported-an-error-filenotfoundexception-46905/)
- [Multipartfile 上传文件使用 transferTo 抛 java.io.FileNotFoundException C:\Users\admin\AppData\Local\Temp_LoveEate的博客-CSDN博客](https://blog.csdn.net/LoveEate/article/details/108579494)
- [MultipartFile的transferTo方法的坑_CrazyDragon_King的博客-CSDN博客_file transferto](https://blog.csdn.net/qq_40734247/article/details/110248410)


## 分析原因

![](https://i.imgur.com/maX8QYw.png)

參考來源:
[一次突如其来的FileNotFoundException -- 大狗小窝](https://blog.zcw159357.com/article/1/2020-05-08-2248.html)


因為 `file.isAbsolute()` 原因會補上`location`絕對位置，會造成抓出路徑錯誤位置。