---
title: Shell 檢查 txt 和 總數確認檔方法
date: 2020-07-19 17:18:29
tags: [shell]
categories: Linux
---

最近要人工分開 txt檔案
因為確認檔案花很多眼力去做調整
偶爾會有改錯情形
為了不發生這個原因
我做個簡單調整

<!--more-->


```bash
#!/bin/bash

FILE_LIST=(`ls *.txt *.ctl`)

# echo ${#FILE_LIST[@]};

for i in ${!FILE_LIST[@]}; do
	FILE_LIST[$i]="${FILE_LIST[$i]%.*}"
done

# echo "${FILE_LIST[@]}" | tr ' ' '\n' | sort -u
FILE_LIST=($(echo "${FILE_LIST[@]}" | tr ' ' '\n' | sort -u ))
# echo ${#FILE_LIST[@]};
for i in ${!FILE_LIST[@]}; do
	if [ -f ${FILE_LIST[$i]}.txt ]; then
	    # 檔案 /path/to/dir/filename 存在
	    echo "File ${FILE_LIST[$i]}.txt exists."
	else
	    # 檔案 /path/to/dir/filename 不存在
	    echo "File ${FILE_LIST[$i]}.txt does not exists."
	    continue;
	fi

	if [ -f ${FILE_LIST[$i]}.ctl ]; then
	    # 檔案 /path/to/dir/filename 存在
	    echo "File ${FILE_LIST[$i]}.ctl exists."
	else
	    # 檔案 /path/to/dir/filename 不存在
	    echo "File ${FILE_LIST[$i]}.ctl does not exists."
	    continue;
	fi

	FIRST_LINE=$(head -n 1 ${FILE_LIST[$i]}.ctl)

	LINE_COUNT=$(cat ${FILE_LIST[$i]}.txt | wc -l)
	#https://unix.stackexchange.com/questions/232384/argument-string-to-integer-in-bash/232386
	echo "TXT:    $((LINE_COUNT+1))"
	echo "CTL:    $FIRST_LINE" ;

	if [ "$FIRST_LINE" = $((LINE_COUNT+1)) ]; then
		echo "${FILE_LIST[$i]} 正確"
	else
		echo "${FILE_LIST[$i]} 不正確!!!!!!!!!!!!!!!!!!"
	fi


done
#https://stackoverflow.com/questions/13648410/how-can-i-get-unique-values-from-an-array-in-bash

# 
# line=$(head -n 1 filename)

# https://stackoverflow.com/questions/3137094/how-to-count-lines-in-a-document
# cat /dir/file.txt | wc -l
```