---
title: "Word,Excel的相關template套件小記"
date: 2019-05-05 20:07:36
tags: [word, excel, template]
categories: 實用工具
---

最近朋友問我有沒有用程式方法套進去 Word
不過我平常沒有這個需求
我是有跟他提到有 合併列印、VBA
但是查了一下
竟然發現找到[【茶包射手日記】純 JavaScript DOCX 套版元件 Debug-黑暗執行緒](https://blog.darkthread.net/blog/pure-js-docx-template-print/?fbclid=IwAR10as2G6yN_tM4_dsY_TEFOjdA-OIXq86hG5Qe7hS6SZNd2pxZYA6loynA)
原來網頁版 js 就能做到這種事

<!--more-->

但我覺得應該還有很多東西
所以整理一下

因為我有先找到[js-excel-template - npm](https://www.npmjs.com/package/js-excel-template)
內容兩個圖片，就讓我想馬上測試官網的範例
首先有很複雜的表不建議使用這個

1. sheet 多筆資料不會套用
2. 塞多筆 table 資料，可能下面用總計，不會自動往下移
3. Excel 有設定一些格式不會用

測試程式

```test_excel.js
const JsExcelTemplate = require("js-excel-template/nodejs/nodejs").default;
const excelTemplate = JsExcelTemplate.fromFile("test.xlsx");

excelTemplate.set("name", "John");
excelTemplate.set("age", 123);
excelTemplate.set("now", new Date());
excelTemplate.set("isBoy", true);
excelTemplate.set("isGirl", false);

const students = [
  { name: "Tommy", age: 12 },
  { name: "Philips", age: 13 },
  { name: "Sara", age: 14 },
  { name: "中文測試", age: 14 }
];

for (let i = 1; i <= 5; i++) {
  if (i <= students.length) {
    excelTemplate.set(`name${i}`, students[i - 1].name);
    excelTemplate.set(`age${i}`, students[i - 1].age);
  } else {
    excelTemplate.set(`name${i}`, "");
    excelTemplate.set(`age${i}`, "");
  }
}
excelTemplate.set(
  "average",
  students.reduce((p, c) => p + c.age, 0) / students.length
);

excelTemplate.set("students", students);

// nodejs:
excelTemplate.saveAs("out.xlsx");
```

雖然不推薦，但適合做單頁式報表

- [Templater - reporting Library for .NET and Java](https://templater.info/)
- [GitHub - wilson6/NPOI-Template: C# 根据 NPOI 模版去生成复杂的 EXCEL 表格，标签化。](https://github.com/wilson6/NPOI-Template)
- [GitHub - optilude/xlsx-template: A NodeJS module to generate Excel files in .xlsx format from a template created with Excel itself](https://github.com/optilude/xlsx-template)
- [GitHub - ivahaev/go-xlsx-templater: Simple xlsx template based document generator](https://github.com/ivahaev/go-xlsx-templater)
- [GitHub - ngs-doo/TemplaterExamples: Creating reports in .NET and Java](https://github.com/ngs-doo/TemplaterExamples)
- [GitHub - anmoroz/xlsx-template-php: A library for templating of Excel Office documents. Supports MS Excel xlsx. Uses PHPExcel library.](https://github.com/anmoroz/xlsx-template-php)
- [GitHub - optimistex/xlsx-template-ex: The template engine for generate xlsx documents](https://github.com/optimistex/xlsx-template-ex)
- [poi-tl](http://deepoove.com/poi-tl/)
- [GitHub - shadz3rg/PHPStamp: The XSL-way templating library for MS Office Word DOCX documents.](https://github.com/shadz3rg/PHPStamp)
- [GitHub - guigrpa/docx-templates: Template-based docx report creation](https://github.com/guigrpa/docx-templates)
- [GitHub - legion-zver/go-docx-templates](https://github.com/legion-zver/go-docx-templates)
- [GitHub - Skrol29/opentbs: With OpenTBS you can merge OpenOffice - LibreOffice and Ms Office documents with PHP using the TinyButStrong template engine. Simple use OpenOffice - LibreOffice or Ms Office to edit your templates : DOCX, XLSX, PPTX, ODT, OSD, ODP and other formats. That is the Natural Template philosophy.](https://github.com/Skrol29/opentbs)
- [GitHub - guigrpa/docx-templates: Template-based docx report creation](https://github.com/guigrpa/docx-templates)

... 太多了，不列了

目前[GitHub - Skrol29/opentbs: With OpenTBS you can merge OpenOffice - LibreOffice and Ms Office documents with PHP using the TinyButStrong template engine. Simple use OpenOffice - LibreOffice or Ms Office to edit your templates : DOCX, XLSX, PPTX, ODT, OSD, ODP and other formats. That is the Natural Template philosophy.](https://github.com/Skrol29/opentbs)、[GitHub - optilude/xlsx-template: A NodeJS module to generate Excel files in .xlsx format from a template created with Excel itself](https://github.com/optilude/xlsx-template)這幾個星星數比較多

[Exwog - from Excel to Word reports generator (xls to doc, xlsx to docx) by template](https://exwog.com/)
[GitHub - HY-Org/hy.common.report: 报表、Excel 操作类库。Java 转 Excel、Excel 转 Java](https://github.com/HY-Org/hy.common.report)
[GitHub - hanzhaoxin/ExcelReport: This reporting engine is built on NPOI.](https://github.com/hanzhaoxin/ExcelReport)
[GitHub - Jimmy-JS/laravel-report-generator: Rapidly Generate Simple Pdf, CSV, & Excel Report Package on Laravel](https://github.com/Jimmy-JS/laravel-report-generator)
[GitHub - biezhi/excel-plus: ❇️ Improve the productivity of the Excel operation library. https://git.io/vNjQy](https://github.com/biezhi/excel-plus)


**2019-09-08**
可以看這篇[php-excel-templator 用範本(template)快速產生Excel方法 | 程式狂想筆記](https://malagege.github.io/blog/2019/09/03/php-excel-templator-%E7%94%A8%E7%AF%84%E6%9C%AC-template-%E5%BF%AB%E9%80%9F%E7%94%A2%E7%94%9FExcel%E6%96%B9%E6%B3%95/)