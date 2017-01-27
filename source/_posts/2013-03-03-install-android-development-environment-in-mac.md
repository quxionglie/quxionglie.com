---
layout: post
title: "Mac下Android开发环境搭建"
date: 2013-03-03 11:33
comments: true
categories: android 
---
基本步骤：   
1. 下载Eclipse IDE   
2. 下载安装配置Android SDK    
3. 安装Android ADT Plugin  
4. Eclipse中配置Android套件  

<!--more-->

# 下载Eclipse IDE 
从这里下载相应的版本： [http://www.eclipse.org/downloads/](http://www.eclipse.org/downloads/)
 
我已经下载好的版本是：eclipse-SDK-4.2.1-macosx-cocoa-x86_64.tar.gz

# 下载安装配置Android SDK  
## 下载配置Android SDK   
地址：http://developer.android.com/sdk/index.html

我当前最新版的下载地址为：  
https://dl.google.com/android/android-sdk_r21.1-macosx.zip


![android](/images/2013/03/android.jpeg)


## 安装配置Android SDK  
上面的sdk包解压到   
/Users/xionglie/tools/android-sdk-macosx

然后运行tools/android,在弹出的Android SDK Manager对话框中选择左边的Installed packages，右边就会列出当前已经安装了的SDK，选择相应组件进行安装，然后一步一步来就会下载所有的Android SDK的版本并进行安装。

![android](/images/2013/03/android1.png)

tools/adb_has_moved.txt内容说明：  
基本意思就是adb已经移到platform-tools,如果在目录中找不到platform-tools，就要执行android工具安装"Android SDK Platform-tools"，并要使PATH环境变量中包含platform-tools/目录，这样就可以在任何目录中执行adb命令了。

```
The adb tool has moved to platform-tools/

If you don't see this directory in your SDK,
launch the SDK and AVD Manager (execute the android tool)
and install "Android SDK Platform-tools"

Please also update your PATH environment variable to
include the platform-tools/ directory, so you can
execute adb from any location.
```

### 配置PATH环境变量
在～/.bash_profile最后一行添加

	export PATH=$PATH:/Users/xionglie/tools/android-sdk-macosx/platform-tools

在控制台输入adb,不出现命令找不到的错误，就算成功

# 安装Android ADT Plugin
启动Eclipse
Help -> Install New Software ->   
site： https://dl-ssl.google.com/android/eclipse/ 

按照提示进行安装


# Eclipse中配置Android套件
重启Eclipse后，Eclipse ->Preferences->Android    

SDK Location : /Users/xionglie/tools/android-sdk-macosx

![android](/images/2013/03/android2.png)

（完）