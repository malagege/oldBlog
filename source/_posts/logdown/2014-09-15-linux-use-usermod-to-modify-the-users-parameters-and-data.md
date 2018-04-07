---
layout: post
title: 'Linux 利用 usermod 修改使用者的參數和資料'
date: 2014-09-15 17:11
comments: true
categories: Linux
tags: [usermod,Linux]
---

>修改使用者帳號資訊（管理者）
 # usermod -l newname username　　　　　# 變更使用者帳號名稱
 # usermod -o -u 101 username　　　　　 # 變更使用者 UID
 # usermod -g users username　　　　　　# 變更使用者主要群組
 # usermod -G mis,sales username　　　　# 變更使用者次要群組
 # usermod -c "manager" username　　　 # 修改使用者註解內容
 # usermod -m -d /home/user username　# 變更使用者家目錄路徑
 # usermod -s /usr/bin/ksh username　　 # 變更使用者預設 Shell
 # usermod -e 1/1/2010 username　　　　# 變更使用者過期期限
 # usermod -p FEeig.jYifke username　　 # 變更使用者密碼


 來源：http://blog.yam.com/gavint/article/23264761


>Linux usermod -p 修改用户密码
---
passwd也可以修改口令，但是这是一种交互式的方式，需要用户干预。当然也可以使用重定向或者管道向passwd喂数据，暂且不提。
linux下增加用户的命令是useradd，修改用户的命令是usermod，二者都有一个参数 –p，这个参数可以直接指定用户的口令，但是需要注意的是，这个口令并不是明文，而是经过加密的一个字串。
linux下可以用python编辑器通过编程的方法得到加密字串（linux下用python命令启动编辑器）：
下面是一个完整的过程，蓝色字体的是用户输入的命令，而红色的是系统输出。
```linux
[root@linux ~]# python
Python 2.3.4 (#1, Oct 26 2004, 16:42:40)
```
```python
[GCC 3.4.2 20041017 (Red Hat 3.4.2-6.fc3)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
 import crypt;print(crypt.crypt("your password","ab"));
ab62Jfo2yjBEo
>>>
```
上面用户输入信息中的“your password”可以替换为需要加密的口令，“ab”可替换为任意的两个大小写字符或者数字，而“ab62Jfo2yjBEo”就是加密后的密文。
一个简单的例子如下：
useradd –p ab62Jfo2yjBEo testuser
usermod –p ab62Jfo2yjBEo testuser


來源：http://www.cppblog.com/aaxron/archive/2010/12/02/135251.html