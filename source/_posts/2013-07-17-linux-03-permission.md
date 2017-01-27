---
layout: post
title: "linux系列3 - 文件与目录权限"
date: 2013-07-17 12:45
comments: true
categories: linux
---

本文的内容如下： 

```  
1.linux权限表示   
1.1 数字表示法  
1.2 字符表示化  

2. linux权限相关命令  
	2.1.改变权限属性命令chmod
	2.2.改变文件所属关系命令chown  

3.linux文件及目录权限总结   
4.权限测试前期准备   
5.测试user1用户浏览非自己拥有的目录/user2的权限  
```

<!-- more -->

# linux权限表示
```
[root@lie-pc ~]# ll 
total 60
-rw------- 1 root root   847 Jul 17 20:43 anaconda-ks.cfg
drwxr-xr-x 2 root root  4096 Jul 19 13:51 dir
-rw-r--r-- 1 root root 36494 Jul 17 20:43 install.log
-rw-r--r-- 1 root root  3849 Jul 17 20:43 install.log.syslog
```
上面第一列的第2个字符开始就是权限位，如anaconda-ks.cfg的rw-------或dir的rwxr-xr-x。 

文件权限概述				
9个权限位,每三位分为一组。分别表示owner,group,other用户的权限   
rwxr--r--	     
r读	  
w写	  
x执行	  
- 不可读，不可写，不可执行   	
权限位有下面两种表示方法.  	

## 数字表示法
chmod [数字组合] 文件名	
r	4	
w	2		
x	1	
-	0	

如：rwxr-xr-x => rwx=4+2+1=7 , r-x=4+0+1=5,故rwxr-xr-x=755。   
每个三位的权限代码组合：  
0	---  
1	--x  
2	–w-  
3	-wx    
4	r--	   
5	r-x			
6	rw-		
7	rwx	

## 字符表示化
chmod [用户类型] [+][-][=] 权限字符 文件名		
用户或用户组定义：u 属主，g 所属用户组, o其它用户，a 全部，包括（ugo）。  		
权限定义： r 读，w 写，x 执行。		
权限增减字符：+ 添加某个权限; - 取消某个权限; = 赋予给定权限并取消其它权限。 
 

# linux权限相关命令
## 改变权限属性命令chmod

chmod改变文件或目录权限的命令，但只有文件属主和超级用户root才有这种权限。 
 
chmod修改权限两种方式：     
(1)	通过字母或操作符表达式  
(2)	通过数字  

对于目录权限的设置，要用到-R参数；        
和数字权限方法一样，如果我们为一个目录及其下的子目录或文件设置相同的属性，就可以用-R参数。 

```
[root@lie-pc ~]# touch file1
[root@lie-pc ~]# touch file2
[root@lie-pc ~]# ll file1 file2 
-rw-r--r-- 1 root root 0 Jul 20 21:38 file1
-rw-r--r-- 1 root root 0 Jul 20 21:38 file2
[root@lie-pc ~]# chmod 755 file1 
[root@lie-pc ~]# chmod u+x,og+x file2 
[root@lie-pc ~]# ls -lh file1 file2 
-rwxr-xr-x 1 root root 0 Jul 20 21:38 file1
-rwxr-xr-x 1 root root 0 Jul 20 21:38 file2
#两种方法都能达到相同的目的。
```
 
## 改变文件所属关系命令chown

当我们要改变一个文件的属组，我们所使用的用户必须是该文件的属主而且同时是目标属组成员或超级用户。只有超级用户才能改变文件的属主。

-R参数：改变目录下所有文件和目录的所有者和组。
说明：chown所接的新的属主和新的属组之间应该以.或:连接，属主和属组任意之一可以为空。如果属主为空，应该是 :属组 ,如果属组为空，就不必要.或:了。


```
#chown [选项]… [所有者][:[组]] 文件
[root@lie-pc ~]# ls -ld /user1
drwxr-xr-x 2 user1 user1 4096 Jul 18 12:53 /user1
[root@lie-pc ~]# chown user1:root /user1  	#:也可以为.号
[root@lie-pc ~]# ls -ld /user1
drwxr-xr-x 2 user1 root 4096 Jul 18 12:53 /user1
[root@lie-pc ~]# chown user1 /user1		#只修改用户
[root@lie-pc ~]# chown .root /user1/file 	#只修改用户组
注：要修改的用户和组必须是系统中已经存在的。
```

# linux文件及目录权限总结
## 普通文件rwx说明
r 可阅读文件内容的权限;      
w 新增、修改文件内容的权限；（特别提示：删除、修改、移动目录内文件的权限受父目录的权限控制）；        
x 文件可被执行的权限    
## 目录rwx权限说明
x 进入该目录成为工作目录的权限;	   
r 读取目录结构列表的权限;	   
w 更改目录结构的权限，也就是有下面一些权限：	   
(1)	新建新的文件与目录;	   
(2)	删除已存在的文件与目录（不论该文件的权限为何）;	 
(3)	将已存在的文件或目录进行重命名；	
(4)	转移该目录内的文件、目录位置;	
总之目录的w权限与该目录下面的文件名变动有关。	
## 文件和目录rwx权限对比

r 读	
文件：有阅读文件内容权限	
目录：浏览目录的权限(注意：与进入目录权限不同)	

w 写	
文件：新增修改文件内容（注意：删除、修改、移动目录内文件和文件本身属性无关）  
目录：表示具有删除、移动、修改目录内文件的权限。如果要在目录中创建、移动、删除文件或目录必须有x权限。  

x 执行	
文件：执行文件的权限	
目录：进入目录的权限。	
- 无任何权限	

特别注意：当删除或移动一个文件或目录时，仅与该文件与目录的上一层目录有关,与该文件本身的属性无关。对文件来说，写文件是修改文件，而不是删除文件，因此写文件与该文件的本身属性有关。

# 权限测试前期准备


## 添加两个普通用户：user1, user2


```
[root@lie-pc ~]# useradd user1
[root@lie-pc ~]# useradd user2
```


## 建立两个目录，并设置归属用户和组
```
[root@lie-pc ~]# mkdir /user1
[root@lie-pc ~]# mkdir /user2
[root@lie-pc ~]# chown user1.user1 /user1
[root@lie-pc ~]# chown user2.user2 /user2
```
## /user1, /user2目录下分别建file文件

```
[root@lie-pc ~]# touch /user1/file
[root@lie-pc ~]# touch /user2/file

 #查看新建目录和文件的权限 
[root@lie-pc ~]# ls -ld /user1 /user2			
drwxr-xr-x 2 user1 user1 4096 Jul 18 12:53 /user1
drwxr-xr-x 2 user2 user2 4096 Jul 18 12:53 /user2
#目录默认的权限是755

[root@lie-pc ~]# ls -l /user1/file /user2/file    
-rw-r--r-- 1 root root 0 Jul 18 12:53 /user1/file
-rw-r--r-- 1 root root 0 Jul 18 12:53 /user2/file
#文件默认的权限是644
```

# 测试user1用户浏览非自己拥有的目录/user2的权限

## 用户user1对目录/user2来说属于其它组，所以只看后三位的权限r-x。所以user1对目录/user2有读(浏览目录)和执行(进入目录)的权限。

```
[root@lie-pc ~]# su - user1
[user1@lie-pc ~]$ ls -ld /user2
drwxr-xr-x 2 user2 user2 4096 Jul 18 12:53 /user2

#用户user1，可以浏览/user2的文件
[user1@lie-pc ~]$ ls -l /user2
total 0
-rw-r--r-- 1 root root 0 Jul 18 12:53 file

[user1@lie-pc ~]$ cd /user2/
[user1@lie-pc user2]$			#用户user1，可以进入目录/user2

 #user1删除非自身属主的文件权限
[user1@lie-pc user2]$ rm -f file 
rm: cannot remove `file': Permission denied

#权限修改为777看
[root@lie-pc ~]# chmod 777 /user2/file 
[root@lie-pc ~]# ls -l /user2
total 0
-rwxrwxrwx 1 root root 0 Jul 18 12:53 file

[user1@lie-pc user2]$ ll
total 0
-rwxrwxrwx 1 root root 0 Jul 18 12:53 file
[user1@lie-pc user2]$ rm -f file
rm: cannot remove `file': Permission denied
#还是权限不够
```
总结:  
删除或移动一个文件时，与该文件的上层目录有关，与文件本身的属性无关。即使文件的权限是777也不可以。

```
[root@lie-pc ~]# chmod 755 /user2
[root@lie-pc ~]# chmod o+w  /user2
[root@lie-pc ~]# ls -ld /user2 
drwxr-xrwx 2 user2 user2 4096 Jul 18 12:53 /user2
[root@lie-pc ~]# chmod 644 /user2/file 
[root@lie-pc ~]# ls -l /user2
total 0
-rw-r--r-- 1 root root 0 Jul 18 12:53 file

[user1@lie-pc ~]$ ls -ld /user2
drwxr-xrwx 2 user2 user2 4096 Jul 18 12:53 /user2
[user1@lie-pc ~]$ ls -l /user2
total 0
-rw-r--r-- 1 root root 0 Jul 18 12:53 file
[user1@lie-pc ~]$ cd /user2/
[user1@lie-pc user2]$ ll
total 0
-rw-r--r-- 1 root root 0 Jul 18 12:53 file
[user1@lie-pc user2]$ rm -f file 		#删除成功

[root@lie-pc ~]# cd /user2
[root@lie-pc user2]# touch file2
[root@lie-pc user2]# chmod 000 file2 		#设置file2无任何权限
[root@lie-pc user2]# ls -l file2 
---------- 1 root root 0 Jul 19 12:38 file2

#user1操作
[user1@lie-pc user2]$ ls -l /user2
total 0
---------- 1 root root 0 Jul 19 12:38 file2
[user1@lie-pc user2]$ rm -f file2 
[user1@lie-pc user2]$ ls -l /user2		#也能删除成功
total 0
```

## 测试user1仅有w权限是否能删除/user2内的文件
```
[root@lie-pc ~]# chmod o=w /user2		#其它用户仅有w权限
[root@lie-pc ~]# ls -ld /user2
drwxr-x-w- 2 user2 user2 4096 Jul 19 12:39 /user2
[root@lie-pc ~]# touch /user2/file
[root@lie-pc ~]# ls -l /user2
total 0
-rw-r--r-- 1 root root 0 Jul 19 12:43 file

#user1操作
[user1@lie-pc ~]$ ls -l /user2
ls: /user2: Permission denied
[user1@lie-pc ~]$ cd  /user2 
-bash: cd: /user2: Permission denied
[user1@lie-pc ~]$ rm -f /user2/file
rm: cannot remove `/user2/file': Permission denied
```
总结：仅有w权限，user1是无法删除/user2/file的

## user1有rw权限看是否能删除/user2内的文件
```
[root@lie-pc ~]# chmod 756 /user2
[root@lie-pc ~]# ls -ld /user2
drwxr-xrw- 2 user2 user2 4096 Jul 19 12:43 /user2

#user1操作
[user1@lie-pc ~]$ ls -ld /user2/ 
drwxr-xrw- 2 user2 user2 4096 Jul 19 12:43 /user2/
[user1@lie-pc ~]$ ls -l /user2/
total 0
?--------- ? ? ? ?            ? file
[user1@lie-pc ~]$ rm -f /user2/file 
rm: cannot remove `/user2/file': Permission denied
```	
总结：仅有rw权限，user1依然无法删除/user2/file

```

##测试user1仅有wx权限看是否能删除/user2内的文件
```
[root@lie-pc ~]# chmod 753 /user2
[root@lie-pc ~]# ls -ld /user2
drwxr-x-wx 2 user2 user2 4096 Jul 19 12:43 /user2
[root@lie-pc ~]# ls -l /user2
total 0
-rw-r--r-- 1 root root 0 Jul 19 12:43 file

#user1操作
[user1@lie-pc ~]$ ls -ld /user2/
drwxr-x-wx 2 user2 user2 4096 Jul 19 12:43 /user2/
[user1@lie-pc ~]$ ls -l /user2/ 
ls: /user2/: Permission denied
[user1@lie-pc ~]$ cd /user2
[user1@lie-pc user2]$ rm -f file	#删除成功
[user1@lie-pc user2]$ 
```

结论：    
1）	删除或移动一个文件，与该文件的上层目录的权限有关，与该文件本身的属性无关，即使是777也不可能删除或移动!     
2）	对于目录，可写w表示具有修改或删除目录内文件的权限，但必须同时有x权限才可以。  



