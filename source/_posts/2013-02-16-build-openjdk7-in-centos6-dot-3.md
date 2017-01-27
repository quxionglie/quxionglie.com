---
layout: post
title: "Centos6.3上编译OpenJDK7源码"
date: 2013-02-16 15:56
comments: true
categories: java
---

本文包括4部分内容：   
1.基本流程（阅读README和README-builds.html）  
2.安装基础软件包  
3.配置变量  
4.检查环境是否配置ok与编译jdk源码  

<!-- more -->

下载源码openjdk-7u6-fcs-src-b24-28_aug_2012.zip，解压

## 基本流程（阅读README和README-builds.html）
README-builds.html中包含有详细的安装信息，最好能完整的阅读一下。  
---------README--------  
Simple Build Instructions:

0.Get the necessary system software/packages installed on your system, see
http://hg.openjdk.java.net/jdk7/build/raw-file/tip/README-builds.html  

1.If you don't have a jdk6 installed, download and install a JDK 6 from
http://java.sun.com/javase/downloads/index.jsp
Set the environment variable ALT_BOOTDIR to the location of JDK 6.

2.Check the sanity of doing a build with your current system:
make sanity
See README-builds.html if you run into problems.

3.Do a complete build of the OpenJDK:
make all
The resulting JDK image should be found in build/*/j2sdk-image

----------README-builds.html---------  
Basic Linux Check List

Install the Bootstrap JDK, set ALT_BOOTDIR.  
Optional Import JDK, set ALT_JDK_IMPORT_PATH.	
Install or upgrade the FreeType development package.  
Install Ant 1.7.1 or newer, make sure it is in your PATH.  

### CentOS 5.5

After installing CentOS 5.5 you need to make sure you have the following   Development bundles installed:  
	Development Libraries  
 	Development Tools  
 	Java Development  
 	X Software Development (Including XFree86-devel)  
Plus the following packages:   

 cups devel: Cups Development Package  
 alsa devel: Alsa Development Package  
 ant: Ant Package  
 Xi devel: libXi.so Development Package  
The freetype 2.3 packages don't seem to be available, but the freetype 2.3 sources can be downloaded, built, and installed easily enough from the freetype site. Build and install with something like:  

``` bash
./configure && make && sudo -u root make install 
```

(注意：Version 2.3 or newer of FreeType is required for building the OpenJDK.) =》freetype的版本至少为2.3或更新  

Mercurial packages could not be found easily, but a Google search should find ones, and they usually include Python if it's needed.  

注意：    
(1).All OpenJDK builds require access to least Ant 1.7.1.（ant的版本至少为1.7.1）  
(2).Linux only: Version 0.9.1 or newer of the ALSA files are required for building the OpenJDK on Linux. 

## 安装基础软件包
我的centos6安装在vmware Fusion上，安装时使用最小化(Minimal)安装

```bash
[root@vm-pc ~]# cat /etc/redhat-release 
CentOS release 6.3 (Final)
[root@vm-pc ~]# uname -m
x86_64
[root@vm-pc ~]# uname -r
2.6.32-279.el6.x86_64
```

执行下面命令

```bash
#!/bin/bash

#配置更新源
cd /etc/yum.repos.d/
curl http://mirrors.163.com/.help/CentOS6-Base-163.repo > CentOS6-Base-163.repo 
#当前wget还不能用
#wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
mv CentOS-Base.repo CentOS-Base.repo.bak
mv CentOS6-Base-163.repo CentOS-Base.repo
yum makecache

yum -y groupinstall 'base'
yum -y install make


#安装jdk必备软件包
yum -y install alsa-lib-devel
yum -y install cups-devel
yum -y install libXi-devel
yum -y install gcc gcc-c++
yum -y install libX*

mkdir -p /application/tools
cd /application/tools

#上传或下载相关文件到/application/tools
#freetype-2.3.12.tar.gz 
#openjdk-7u6-fcs-src-b24-28_aug_2012.zip
#apache-ant-1.7.1-bin.zip 
#jdk-6u26-linux-i586.bin

#编译安装freetype
tar -xzf freetype-2.3.12.tar.gz
cd freetype-2.3.12
./configure 
make
make install

```

如果安装时出现如下错误，则执行

mkdir -p /usr/local/include/freetype2/freetype/internal  
make install  

rmdir /usr/local/include/freetype2/freetype/internal  
rmdir: failed to remove `/usr/local/include/freetype2/freetype/internal': No such file or directory
make: [install] Error 1 (ignored)
/usr/bin/install -c -m 644 ./builds/unix/ft2unix.h \
/usr/local/include/ft2build.h
/usr/bin/install -c -m 644 ./builds/unix/ftconfig.h \
/usr/local/include/freetype2/freetype/config/ftconfig.h

```bash
#!/bin/bash

#安装jdk
yum -y install ld-linux.so.2
sh install_jdk.sh
source /etc/profile

#安装ant
cd /application/tools/
unzip apache-ant-1.7.1-bin.zip
ln -s /application/tools/apache-ant-1.7.1/bin/ant /usr/bin/ant
```

检查java与ant  

```bash
[root@vm-pc ~]# java -version
java version "1.6.0_26"
Java(TM) SE Runtime Environment (build 1.6.0_26-b03)
Java HotSpot(TM) Client VM (build 20.1-b02, mixed mode, sharing)
[root@vm-pc ~]# ant
Buildfile: build.xml does not exist!
Build failed
```

## 配置变量

```bash
unset CLASSPATH
unset JAVA_HOME
export LANG=C
export ALT_BOOTDIR=/application/java/jdk
export ANT_HOME=/application/tools/apache-ant-1.7.1/
export ALT_FREETYPE_LIB_PATH=/usr/local/lib
export SKIP_DEBUG_BUILD=false 
export SKIP_FASTDEBUG_BUILD=true 
export DEBUG_NAME=debug 
export ALT_FREETYPE_HEADERS_PATH=/usr/local/include/freetype2
```

## 检查环境是否配置OK与编译jdk源码

```bash
[root@vm-pc openjdk]# pwd
/application/tools/openjdk
[root@vm-pc openjdk]# make sanity

.......
OpenJDK-specific settings:
FREETYPE_HEADERS_PATH = /usr/local/include/freetype2
ALT_FREETYPE_HEADERS_PATH = /usr/local/include/freetype2
FREETYPE_LIB_PATH = /usr/local/lib
ALT_FREETYPE_LIB_PATH = /usr/local/lib

Previous JDK Settings:
PREVIOUS_RELEASE_PATH = USING-PREVIOUS_RELEASE_IMAGE
ALT_PREVIOUS_RELEASE_PATH = 
PREVIOUS_JDK_VERSION = 1.6.0
ALT_PREVIOUS_JDK_VERSION = 
PREVIOUS_JDK_FILE = 
ALT_PREVIOUS_JDK_FILE = 
PREVIOUS_JRE_FILE = 
ALT_PREVIOUS_JRE_FILE = 
PREVIOUS_RELEASE_IMAGE = /application/java/jdk
ALT_PREVIOUS_RELEASE_IMAGE =
```

Sanity check passed.  #表示ok了

```bash
[root@vm-pc openjdk]# make all ARCH_DATA_MODEL=64 ALLOW_DOWNLOADS=true
…
#-- Build times ----------
Target debug_build
Start 2013-01-07 14:41:38
End 2013-01-07 15:09:46
00:01:23 corba
00:09:56 hotspot
00:00:18 jaxp
00:00:25 jaxws
00:15:33 jdk
00:00:33 langtools
00:28:08 TOTAL
-------------------------
make[1]: Leaving directory `/application/tools/openjdk'
```

查看劳动成果

```bash
[root@vm-pc openjdk]# ./build/linux-amd64/bin/java -version
openjdk version "1.7.0-internal-debug"
OpenJDK Runtime Environment (build 1.7.0-internal-debug-root_2013_01_07_14_31-b00)
OpenJDK 64-Bit Server VM (build 23.2-b09-jvmg, mixed mode)
```

附：安装install_jdk.sh脚本

```bash
#!bin/bash
# auto install jdk
# Filename: install_jdk.sh
# Author: quxl
# Email: xionglie.qu@gmail.com
# 

if [ "$UID" -ne "0" ]; then
    echo "You must be root to run this script! "
    exit 1
fi

TOOL_DIR=/application/tools
JAVA_DIR=/application/java
JAVA_HOME_DIR=${JAVA_DIR}/jdk
SETUP_FILE=jdk-6u26-linux-i586.bin

mkdir -p ${JAVA_DIR}
/bin/cp -f  ${TOOL_DIR}/${SETUP_FILE} ${JAVA_DIR}/${SETUP_FILE}
cd ${JAVA_DIR}
echo -e "\r\n" > yes
chmod u+x ${SETUP_FILE}
./${SETUP_FILE}<yes
rm -f ${JAVA_DIR}/${SETUP_FILE}
rm -f yes

ln -s ${JAVA_DIR}/jdk1.6.0_26 ${JAVA_HOME_DIR}

cat >> /etc/profile <<EOF
#set java environment
JAVA_HOME=${JAVA_HOME_DIR}
export JRE_HOME=${JAVA_HOME_DIR}/jre
export CLASSPATH=.:\$JAVA_HOME/lib:\$JRE_HOME/lib:\$CLASSPATH
export PATH=\$JAVA_HOME/bin:\$JRE_HOME/bin:\$PATH
EOF

source /etc/profile
echo "install Ok..."

```