---
title: PHPExcel 使用小記
date: 2019-08-08 22:17:38
tags: [phpexcel]
categories: PHP
---

最近做報表業務，有包含一些排版，對這些非常不熟
竟然都用了，紀錄一下我覺得實用筆記
PHPExcel 已經被廢棄了，取而代之的是[PHPOffice/PhpSpreadsheet: A pure PHP library for reading and writing spreadsheet files](https://github.com/PHPOffice/PhpSpreadsheet)
但這篇還是以 PHPExcel 為主

<!--more-->

[PHPOffice/PHPExcel: ARCHIVED](https://github.com/PHPOffice/PHPExcel)

PhpSpreadsheet 一些 note 可以看[phpspreadsheet 开发手记 - leestar54 - 博客园](https://www.cnblogs.com/leestar54/p/7786806.html) {% asset_link web1.png 備份圖 %}

## 載入 PHPEXCEL

[PHPExcel/Classes at 1.8 · PHPOffice/PHPExcel](https://github.com/PHPOffice/PHPExcel/tree/1.8/Classes)

```php
/** PHPExcel */
require_once "lib/excel/PHPExcel.php";
/** PHPExcel_IOFactory */
require_once "lib/excel/PHPExcel/IOFactory.php";
```

## worksheet(工作表)設定

```php
// 建立工作表並指定名稱
$index = 0;
$objPHPExcel->setActiveSheetIndex($index);  //設定預設顯示的工作表
// Rename sheet(sheet名稱)
// 重新命名 sheet 名稱
$month_date = substr($ym, 0, 4).".".substr($ym, -2);
$objPHPExcel->getActiveSheet()->setTitle($month_date);
```

## 背景顏色設定

```php
//設定背景顏色為"鮮黃色" (單色)
$objPHPExcel->getActiveSheet()->getStyle("A1:A2")->applyFromArray(
    array(
        "fill" => array(
            "type" => PHPExcel_Style_Fill::FILL_SOLID,
            "color"=> array("rgb" => "FFFF00") ),
    )
);
//設定背景顏色為"淡黃色"
$backcolor = array(
    'fill' => array(
        'type' => PHPExcel_Style_Fill::FILL_SOLID,
        'color'=> array('rgb' => 'FFFFAB')
    ),
);
$objPHPExcel->getActiveSheet()->getStyle("A1")->applyFromArray($backcolor);
$objPHPExcel->getActiveSheet()->getStyle("A3:B3")->applyFromArray($backcolor);
```

## 設定框線格式

```php
// 設定框線格式
// 用陣列設定 全框線
$style_array = array(
    'borders' => array(
        'allborders' => array(
            'style' => PHPExcel_Style_Border::BORDER_THIN,
            'color' => array('rgb' => '000000') ),
    )
);
$objPHPExcel->getActiveSheet()->getStyle("A1:O".$rows_count."")->applyFromArray($style_array);
// 下底線
// 總計很常用到
$bottom = array(
'borders' => array(
    'bottom'  => array(
        'style' => PHPExcel_Style_Border::BORDER_MEDIUM,
        'color' => array('rgb' => '000000') ),
)
);
```

## 字體設定

```php
// 設定字型大小
$objPHPExcel->getActiveSheet()->getStyle("A1")->getFont()->setSize(11);
$objPHPExcel->getActiveSheet()->getStyle("A2:B10")->getFont()->setSize(10);
// 粗體
$objPHPExcel->getActiveSheet()->getStyle("A1:B2")->getFont()->setBold(true);
$objPHPExcel->getActiveSheet()->getStyle("A6:I6")->getFont()->setBold(true);
$objPHPExcel->getActiveSheet()->getStyle("N2")->getFont()->setBold(false);
// 動態算位置技巧
$objPHPExcel->getActiveSheet()->getStyle("A".($rows_count).":O".($rows_count)."")->getFont()->setBold(true);
// 自動換行
$objPHPExcel->getActiveSheet()->getStyle("A3:B3")->getAlignment()->setWrapText(true);
```

## 欄位給予值

```php
//設定A1欄位顯示文字2015-08
$month_date = substr($ym, 0, 4)."-".substr($ym, -2);
$objPHPExcel->getActiveSheet()->setCellValue("A1", $month_date );
```

## 欄位類型

```php
//設定成數字
$objPHPExcel->getActiveSheet()->getCell("B20")->setValueExplicit(1000, PHPExcel_Cell_DataType::TYPE_NUMERIC);
//設定成百分比
$objPHPExcel->getActiveSheet()->getStyle("B20")->getNumberFormat()->setFormatCode(PHPExcel_Style_NumberFormat::FORMAT_PERCENTAGE_00);
```

## 欄位對齊方式

```php
//水平對齊 setVertiacl 垂直對齊 setHorizontal
/*
VERTICAL_CENTER 垂直置中
VERTICAL_TOP
HORIZONTAL_CENTER
HORIZONTAL_RIGHT
HORIZONTAL_LEFT
HORIZONTAL_JUSTIFY
*/
$objPHPExcel->getActiveSheet()->getStyle("A1:B3")->getAlignment()->setHorizontal(PHPExcel_Style_Alignment::HORIZONTAL_CENTER);
$objPHPExcel->getActiveSheet()->getStyle("A1:B3")->getAlignment()->setVertical(PHPExcel_Style_Alignment::VERTICAL_CENTER);
$objPHPExcel->getActiveSheet()->getStyle("A1:A".$rows_count."")->getAlignment()->setHorizontal(PHPExcel_Style_Alignment::HORIZONTAL_CENTER);
$objPHPExcel->getActiveSheet()->getStyle("B1:B".$rows_count."")->getAlignment()->setHorizontal(PHPExcel_Style_Alignment::HORIZONTAL_CENTER);

/**
* 合併儲存格
*/
$objPHPExcel->getActiveSheet()->mergeCells("A1:B1");
// or
$objPHPExcel->setActiveSheetIndex(0)->mergeCells('A1:C1');
/**
* 取消 合併儲存格
**/
// [How to Merge/unmerge cells in PHPExcel | Degree Documentation](https://degreedocumentation.wordpress.com/2012/08/17/how-to-mergeunmerge-cells/)
$objPHPExcel->getActiveSheet()->unmergeCells('A18:E22');
```

## 字體顏色

```php
// 設定字體顏色
/*
這裡有列出可用英文單字表示的顏色，並不多。
COLOR_BLACK
COLOR_BLUE
COLOR_DARKBLUE
COLOR_DARKGREEN
COLOR_DARKRED
COLOR_DARKYELLOW
COLOR_GREEN
COLOR_RED
COLOR_WHITE
COLOR_YELLOW
*/
$gray = array(
'font'  => array(
    'color' => array('rgb' => 'BABABA'),
));
$objPHPExcel->getActiveSheet()->getStyle("I1:I7")->applyFromArray($gray);

$yellow = array(
'fill'  => array(
    'type' => PHPExcel_Style_Fill::FILL_SOLID,
    'color' => array('rgb' => 'FFFF00'),
));
$objPHPExcel->getActiveSheet()->getStyle("F1:F2")->applyFromArray($yellow);
$objPHPExcel->getActiveSheet()->getStyle("I2")->applyFromArray($yellow);
```

## 欄位寬度、高度設定

```php
//設定 cell 高度
// 设置默认行高 getDefaultRowDimension
$objPHPExcel->getActiveSheet()->getDefaultRowDimension()->setRowHeight(12);
$objPHPExcel->getActiveSheet()->getRowDimension(1)->setRowHeight(16);
$objPHPExcel->getActiveSheet()->getRowDimension(2)->setRowHeight(16);
$objPHPExcel->getActiveSheet()->getRowDimension(3)->setRowHeight(36);
//設定的欄位寬度(+0.71才正確)
$objPHPExcel->getActiveSheet()->getColumnDimension('A')->setWidth(17);
$objPHPExcel->getActiveSheet()->getColumnDimension('B')->setWidth(6);
```

## 欄位數值資料格式

```php
// 網底
$objPHPExcel->getActiveSheet()->getStyle("A1")->getFill()->setFillType(PHPExcel_Style_Fill::FILL_PATTERN_LIGHTTRELLIS);

// 設定成數字，並且有千分位逗號
// excel格式設定裡的#：代表數字，多餘的0會去掉。
// excel格式設定裡的0:基本上跟#一樣，但是多餘的0會保留。
$objPHPExcel->getActiveSheet()->getStyle("D4:D".($rows_count)."")->getNumberFormat()->setFormatCode('#,##0');
$objPHPExcel->getActiveSheet()->getStyle("O4:O".($rows_count)."")->getNumberFormat()->setFormatCode('$#,##0');

// 設定成百分比
// 設定拆款百分比30.00%
$objPHPExcel->getActiveSheet()->getStyle("E3:E".($rows_count)."")->getNumberFormat()->setFormatCode(PHPExcel_Style_NumberFormat::FORMAT_PERCENTAGE_00);
$objPHPExcel->getActiveSheet()->getStyle("G3:G".($rows_count)."")->getNumberFormat()->setFormatCode(PHPExcel_Style_NumberFormat::FORMAT_PERCENTAGE_00);
$objPHPExcel->getActiveSheet()->getStyle("H3:H".($rows_count)."")->getNumberFormat()->setFormatCode(PHPExcel_Style_NumberFormat::FORMAT_PERCENTAGE);
$objPHPExcel->getActiveSheet()->getCell('H4')->setValueExplicit('0.3', PHPExcel_Cell_DataType::TYPE_NUMERIC);
```

## 凍結窗格

```php
//凍結窗格
$objPHPExcel->getActiveSheet()->freezePane('A6');
$objPHPExcel->getActiveSheet()->freezePane('D6');
```

## 建立新的

```php
$objPHPExcel->createSheet(); //建立新的工作表
$objPHPExcel->setActiveSheetIndex($index+1);//現編輯頁
$objPHPExcel->getActiveSheet()->setTitle("標題名稱");  //設定標題
```

## 動態處理橫軸資料(A~AZ)

PHPExcel 可以用 `$objPHPExcel->getActiveSheet()->setCellValue("A1", $month_date );`
塞值真的非常方便
但是沒辦法動態產生資料
可以透過`setCellValueByColumnAndRow`
但注意第一個欄(column 英文)是從 0 開始
列是從 1 開始
`$objPHPExcel->getActiveSheet()->setCellValueByColumnAndRow(0, 1,"2019-04月");`

但還有一個問題，就是轉換 getStyle 沒有這類似 function
`PHPExcel_Cell::stringFromColumnIndex` 可以簡單轉換
stringFromColumnIndex(0) = 'A' <=> columnIndexFromString

```php
// 一般
$objPHPExcel->getActiveSheet()->setCellValueByColumnAndRow(0, 1,."2019-04月");
// 合併儲存格
$objPHPExcel->getActiveSheet()->mergeCellsByColumnAndRow( $index, 1, $index + 8 , 1);

// PHPExcel_Cell::stringFromColumnIndex
$objPHPExcel->getActiveSheet()->getStyle(PHPExcel_Cell::stringFromColumnIndex( $index ).'1')->getAlignment()->setHorizontal(PHPExcel_Style_Alignment::HORIZONTAL_CENTER);
$objPHPExcel->getActiveSheet()->getStyle(PHPExcel_Cell::stringFromColumnIndex( $index ).'1')->getAlignment()->setVertical(PHPExcel_Style_Alignment::VERTICAL_CENTER);

// 公式運算方法
$col1 = PHPExcel_Cell::stringFromColumnIndex($index + 1);
$col2 = PHPExcel_Cell::stringFromColumnIndex($index + 2);
$col3 = PHPExcel_Cell::stringFromColumnIndex($index + 3);
$objPHPExcel->getActiveSheet()->setCellValueByColumnAndRow($o2, '4',"={$col1}4+{$col2}4+{$col3}4");
$objPHPExcel->getActiveSheet()->setCellValueByColumnAndRow($o2, '4',"=SUM({$scol}$5:{$scol}$".($nu+4).")");
```

## 匯出 EXCEL

```php
// Export to Excel2007 (.xlsx)
$objWriter = PHPExcel_IOFactory::createWriter($objPHPExcel, "Excel2007");
$objWriter->save('export/filename.xlsx');
```

[Ron, Run ~~: PHPExcel - 如何設定儲存格代號](http://ron314159.blogspot.com/2011/02/phpexcel.html)
[PHP 輸出 Excel ，使用 PHPExcel](https://www.puritys.me/docs-blog/article-33-PHP-%E8%BC%B8%E5%87%BA-Excel-%EF%BC%8C%E4%BD%BF%E7%94%A8PHPExcel.html)
[PHP excel 設置參數 - IT 閱讀](https://www.itread01.com/content/1507728009.html)

## 其他程式使用方式

Java 使用 POI 方式
但我覺得不好用....

JavaScript 有找到 js-xlsx，做匯入資料感覺非常方便
一般報表還可以，複雜格式報表可能不行
但是進階設定需要付費，所以還是很可惜

[Node 读写 Excel 文件探究实践 | Aotu.io「凹凸实验室」](https://aotu.io/notes/2016/04/07/node-excel/index.html)
[【node+demo】使用 xlsx-style 设置表格的宽高等样式 - 简书](https://www.jianshu.com/p/877631e7e411)
[使用 js-xlsx 纯前端导出 excel - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000019700368)
不知道為什麼範例沒辦法用出顏色

網路文章:

- [Merge Cell values with PHPExcel - PHP - Stack Overflow](https://stackoverflow.com/questions/6820250/merge-cell-values-with-phpexcel-php)
- [PHP 輸出 Excel ，使用 PHPExcel](https://www.puritys.me/docs-blog/article-33-PHP-%E8%BC%B8%E5%87%BA-Excel-%EF%BC%8C%E4%BD%BF%E7%94%A8PHPExcel.html)
- [基于 Excel 模板导出——ExcelTemplate - gezhonglei2007 的专栏 - CSDN 博客](https://blog.csdn.net/gezhonglei2007/article/details/51475059)
- [phpspreadsheet 开发手记 - leestar54 - 博客园](https://www.cnblogs.com/leestar54/p/7786806.html)
- [columnIndexFromString and stringFromColumnIndex are not compatible · Issue #307 · PHPOffice/PHPExcel](https://github.com/PHPOffice/PHPExcel/issues/307)
- [merge cells from Array of Arrays · Issue #964 · SheetJS/js-xlsx](https://github.com/SheetJS/js-xlsx/issues/964)
- [纯前端利用 js-xlsx 之单元格样式(4) - 简书](https://www.jianshu.com/p/869375439fee)
- [PHPExcel Notes and code snippets](https://gist.github.com/yajra/a71d030e68126714e88a)

公司有遇到RAM 吃很大的問題
[phpExcel導出文件時內存溢出的問題 - Cocowool - 博客園](https://www.cnblogs.com/cocowool/p/6893774.html)