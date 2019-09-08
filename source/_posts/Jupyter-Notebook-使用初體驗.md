---
title: Jupyter Notebook 使用初體驗
date: 2019-08-30 15:34:25
tags: [jupyter]
categories: 實用工具
---

最近看到有人拿 Jupyter 做筆記
之前都不知清楚有什麼作用
不過有看到有人拿來做一些程式筆記
裡面也可以寫 Markdown 文件
今天就來安裝玩玩

<!--more-->

## anaconda

裡面包含 Jupyter Notebook ,pandas....
所以我選擇安裝這個
快速完成環境

PS: Apache Zeppelin 也有類似的功能
## 安裝套件


[jupyter notebook 可以做哪些事情？ - 知乎](https://www.zhihu.com/question/46309360)

### Snippets

因為 python 不熟，安裝這個可以快速使用

#### 安裝方法

```bash
git clone git://github.com/moble/jupyter_boilerplate
jupyter nbextension install jupyter_boilerplate
jupyter nbextension enable jupyter_boilerplate/main
```


### 自動補字

#### 安裝方法

**安裝前記得要先把 jupyter 關掉!!!!**
> 安裝
> 如果之前安裝過顯示目錄功能的話，這一步驟可以跳過。
> pip install jupyter_contrib_nbextensions
> 配置
> 安裝完之後需要配置 nbextension，注意配置的時候要確保已關閉 Jupyter Notebook：
> jupyter contrib nbextension install --user --skip-running-check
> 啟動 Jupyter Notebook，勾選設置
> 上面兩個步驟都沒報錯後，啟動 Jupyter Notebook，上面選項欄會出現 Nbextensions 的選項
> 作者：caoqi95
> 链接：https://www.jianshu.com/p/0ab80f63af8a
> 来源：简书
> 简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。


[Jupyter Notebook 添加代码自动补全功能 - 简书](https://www.jianshu.com/p/0ab80f63af8a)




## csv 轉 xlsx

pandas 可以方便做很多東西
例如資料轉換格式
真的非常方便許多

```python
from __future__ import print_function, division
import pandas as pd

bp_data = pd.read_csv("FL_insurance_sample.csv")

bp_data.to_excel('test.xlsx',index=False)
```

{% asset_link csv轉xlsx.ipynb ipynb檔案 %}

參考範例:[【python】pandas库pd.to_excel操作写入excel文件参数整理与实例 - brucewong0516的博客 - CSDN博客](https://blog.csdn.net/brucewong0516/article/details/79097909)
[Sample CSV Data – SpatialKey Support](https://support.spatialkey.com/spatialkey-sample-csv-data/)
csv來源從這邊來


## pandas 一些應用

可以抓取重複資料，但不知道大檔案處理速度不知道能不能負荷

[pandas使用技巧【18】找到和移除重复行 - 简书](https://www.jianshu.com/p/c033f89f4750)
[資料處理：用pandas處理大型csv檔案 - IT閱讀](https://www.itread01.com/content/1541375289.html)
[Pandas之drop_duplicates：去除重复项 - OraYang的博客 - CSDN博客](https://blog.csdn.net/u010665216/article/details/78559091)
[python - how to transform pandas dataframe for insertion via executemany() statement? - Stack Overflow](https://stackoverflow.com/questions/29938613/how-to-transform-pandas-dataframe-for-insertion-via-executemany-statement)
[How to write python sql output into CSV using a dataframe - Stack Overflow](https://stackoverflow.com/questions/41650188/how-to-write-python-sql-output-into-csv-using-a-dataframe)
[python - SQL statement for CSV files on IPython notebook - Stack Overflow](https://stackoverflow.com/questions/36431213/sql-statement-for-csv-files-on-ipython-notebook)
[【精心解读】关于Jupyter Notebook的28个技巧 - 知乎](https://zhuanlan.zhihu.com/p/32600329)

類似 SQL 應用
[How to rewrite your SQL queries in Pandas, and more](https://medium.com/jbennetcodes/how-to-rewrite-your-sql-queries-in-pandas-and-more-149d341fc53e)


[python 3.x - Removing duplicates between multiple large CSV files - Stack Overflow](https://stackoverflow.com/questions/51824732/removing-duplicates-between-multiple-large-csv-files)


常常有時候會收到一些 Excel 要產生 SQL 語句
但是看一下內建to_sql
好像直接對csv匯入...
可能還是需要用
有機會應用在補充

