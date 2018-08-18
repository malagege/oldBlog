---
title: JAVA poi操作excel文件
date: 2018-08-15 20:58:36
tags: [java,poi,excel]
categories: Java
---

最近做報表用到POI
有踩到一個雷，由於真的不是很熟POI
所以趕快來記我踩的雷

<!--more-->

[poi操作excel文件 - CSDN博客](https://blog.csdn.net/caihaijiang/article/details/6381948)
[HSSF、XSSF和SXSSF区别以及Excel导出优化 - PC君 - 博客园](http://www.cnblogs.com/pcheng/p/7485979.html)

可能踩雷，未知`java.lang.IllegalArgumentException: Sheet index (-1) is out of range (0..2)`問題
經查詢是這段問題
`workbook.setSheetName( sheetIndex , userType == 0 ? label : label + xxxxxx);`
```java
//下面是原寫法
//sheet = workbook.getSheet("XXXXX");

//下面是修改寫法
sheet = workbook.getSheetAt(sheetIndex);

System.out.println("sheetIndex:" + workbook.getSheetIndex(sheet));

String sheetName = userType == 0 ? label : label + xxxxxx;
// 按不同方案別命名Sheet
workbook.setSheetName(sheetIndex, sheetName);

System.out.println("sheetName:" + sheetName);

// 按不同方案別命名Sheet
workbook.setSheetName( sheetIndex , userType == 0 ? label : label + xxxxxx);
```

createRow  會覆蓋當下row
```java

        // 由於資料過少的話，temple Excel欄位header會印出來，所以目前先用小於五筆資料，新增5列資料去覆蓋
        if (arrayList.size() <= 5)
        {
            for (int i = 0; i < arrayList.size(); i++)
            {
                int idxrow = i + 1 + arrayList.size();
                XSSFRow row1 = sheet.createRow(idxrow);

                // 固定內容部分
                XSSFCell cell_1 = row1.createCell(0);
                cell_1.setCellType(XSSFCell.CELL_TYPE_STRING);
                cell_1.setCellValue("");
                XSSFCell cell_2 = row1.createCell(1);
                cell_2.setCellType(XSSFCell.CELL_TYPE_STRING);
                cell_2.setCellValue("");
                XSSFCell cell_3 = row1.createCell(2);
                cell_3.setCellType(XSSFCell.CELL_TYPE_STRING);
                cell_3.setCellValue("");
                // 變動欄位
                XSSFCell cell_4 = row1.createCell(3);
                cell_4.setCellType(XSSFCell.CELL_TYPE_STRING);
                cell_4.setCellValue("");
                XSSFCell cell_5 = row1.createCell(4);
                cell_5.setCellType(XSSFCell.CELL_TYPE_STRING);
                cell_5.setCellValue("");
                XSSFCell cell_6 = row1.createCell(5);
                cell_6.setCellType(XSSFCell.CELL_TYPE_STRING);
                cell_6.setCellValue("");

            }
```

[POI ，Java 操作 Excel 实现行的插入（insert row） - CSDN博客](https://blog.csdn.net/daemon_boy/article/details/1786384)
[POI实现插入一行操作，就像office excel插入一行的操作 - cai555 - ITeye博客](http://cai555.iteye.com/blog/202963)
[NPOI 插入行[转] - kingang - 博客园](https://www.cnblogs.com/kingangWang/archive/2011/08/31/2161319.html)
[Java POI(第一讲):POI中HSSF用法大全-进击的程序猿-51CTO博客](http://blog.51cto.com/zangyanan/1837229)




好想寫WEB orz.......