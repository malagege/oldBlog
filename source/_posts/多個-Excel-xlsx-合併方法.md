---
title: 多個 Excel(xlsx) 合併方法
date: 2021-03-31 20:30:25
tags: [pandas,xlsx,excel merge]
categories: 實用工具
---

平常維運大量資料，需要分批去撈資料
需要把資料合併又要花很多工
最近在找有什麼好用的方法

<!--more-->

## Excel Merger(付費)

https://www.azofreeware.com/2020/06/excel-merger.html

![](https://i.imgur.com/xDd8DbS.png)

免費版會有 Excel Merger Free 字眼，所以應該沒辦法免費使用...


## pandas 

建議直接安裝Anaconda3 


```

import pandas as pd
import numpy as np

--
!dir /D *xlsx
# window 沒 ls
---
import glob
glob.glob("*xlsx")

---

all_data = pd.DataFrame()
for f in glob.glob("*xlsx"):
    df = pd.read_excel(f)
    all_data = all_data.append(df,ignore_index=True)
    
---    

all_data.to_excel("result.xlsx")
```

參考: [Combining Data From Multiple Excel Files - Practical Business Python](https://pbpython.com/excel-file-combine.html)

