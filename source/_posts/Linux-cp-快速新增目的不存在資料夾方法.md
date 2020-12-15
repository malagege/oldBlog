---
title: Linux cp 快速複製目的不存在資料夾方法
date: 2020-09-11 15:30:30
tags: [linux, cp]
categories: Linux
---

之前就有在查類似指令
但沒有覺得很快速方法
這邊先整理

<!--more-->

## mac cp --parents

可用這個指令解決我需求問題
但我沒 mac QQ

## mkdir -p

平常我快速建置資料夾
mkdir -p `dirname /xxxx/xx/x/x/x/x/filename`
cp xxx /xxxx/xx/x/x/x/x/filename

但數量越多，這樣做也不會比較快

## cp-p SOURCE... DIRECTORY/

```bash
cp-p() {
  last=${@: -1}

  if [[ $# -ge 2 && "$last" == */ ]] ; then
    # cp SOURCE... DEST/
    mkdir -p "$last" && cp "$@"
  else
    echo "cp-p: (copy, creating parent dirs)"
    echo "cp-p: Usage: cp-p SOURCE... DEST/"
  fi
}
```

但這樣改從清單還要多一層處理目的資料夾
我覺得不是很實用

## install -D

install -D test1.sh ./xxx/test.sh      

~~但不確定什麼問題，沒選出最佳答案~~

### install 指定清單做複製方法

最近因為專案是用 SVN
但包檔要給客戶需要包成zip
這邊有時候從 SVN 去看目錄手動複製真的很麻煩
尤其還要對 class去做複製

今天寫了好用腳本
Windows 10 直接用 WSL 也可以操作
```bash
#!/bin/bash
# 包版日期
DAYV="201113"
# 包版版本
VERSION="002"

# 有針對資料夾和檔案都可以做複製
# 萬用字玩也可以
# 找不檔案也不會有問題
SPATHLIST=(
# #SHARE
./web/build/classes/aa
./web/build/classes/aa/
# BATCH
./TEST
./batch/src/com/abc/batch/Aaaaa.java
./batch/src/com/abc/batch/schedule/job/*
# bin, class
./batch/bin/com/abc/batch/Aaaaa.class
)

# install 資料夾複製方法參考
# https://unix.stackexchange.com/questions/165463/problem-with-install-command-to-copy-a-whole-directory

DPATH="/CodePackage/${DAYV}/${VERSION}/${DAYV}_code_package"

length=${#SPATHLIST[@]}
for(( j=0; j<$length; j++ ))
do
	if [[ ! -e "${SPATHLIST[$j]}" ]]; then # 檢查檔案 https://linuxize.com/post/bash-check-if-file-exists/
		echo "${SPATHLIST[$j]}找不到檔案檔案"
		continue
	fi
	if [[ -d "${SPATHLIST[$j]}" ]]; then
    # 加入 exec 印出內容
		# https://askubuntu.com/questions/651315/how-do-you-output-the-filename-in-find-command-while-using-exec
		find "${SPATHLIST[$j]}" -type f -printf '%p\n' -exec install -D  "{}" "${DPATH}/{}" \;
	else
		install -D ${SPATHLIST[$j]} ${DPATH}/${SPATHLIST[$j]}	
	fi
	echo "${SPATHLIST[$j]} 成功搬移 ${DPATH}/${SPATHLIST[$j]}"
done

cp ./copyfiles.sh ${DPATH}/../copyfiles${DAYV}${VERSION}.sh

# 寫更版紀錄 txt
TXT_FILE="更版紀錄_${DAYV}_${VERSION}.txt"
TXT_FILEPATH=${DPATH}/${TXT_FILE}

echo "<${DAYV}_${VERSION}> #" > ${TXT_FILEPATH}
echo "" >> ${TXT_FILEPATH}
echo "Revision: >>>>>> (Beta${VERSION})" >> ${TXT_FILEPATH}
echo "Author: xxx" >> ${TXT_FILEPATH}
echo "Date: $(date +"%Y年%m月%d日 %T")" >> ${TXT_FILEPATH}
echo "" >> ${TXT_FILEPATH}
echo " #>>>>>>" >> ${TXT_FILEPATH}
echo "" >> ${TXT_FILEPATH}
echo "【問題說明】" >> ${TXT_FILEPATH}
echo "     >>>>>>" >> ${TXT_FILEPATH}
echo "【處理說明】" >> ${TXT_FILEPATH}
echo "     >>>>>>" >> ${TXT_FILEPATH}
echo "     " >> ${TXT_FILEPATH}
echo ""  >> ${TXT_FILEPATH}
echo "相關程式如下："  >> ${TXT_FILEPATH}
echo ""  >> ${TXT_FILEPATH}
for(( j=0; j<$length; j++ ))
do
	let INDEX=$j+1
	echo "${INDEX}." "${SPATHLIST[$j]}"  >> ${TXT_FILEPATH}
done

# 產生 password txt(為了手動包 zip用的)
# 因為客戶需要更版紀錄，我需要去 Mantis 複製內容再zip
 echo "pwd20"${DAYV} >  ${DPATH}/../password.txt

```

這樣半自動化，相信能減少很多出錯