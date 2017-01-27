---
layout: post
title: "mac新手必备"
date: 2013-01-04 19:46
comments: true
categories: mac
---

mac必备工具集

<!-- more -->

## Alfred(必备)

Alfred is an award-winning productivity application for Mac OS X, which aims to save you time in searching your local computer and the web.

![img01](/images/2013/newer-should-be-known-in-mac/01.png)

![img01](/images/2013/newer-should-be-known-in-mac/02.png)


## OS X 键盘快捷键

完整的快捷键可参考：http://support.apple.com/kb/HT1343?viewlocale=zh_CN  
#####Mac截图快捷键 
来源：http://www.dnzs123.com/html/os/mac/672.html

全屏截图：Command-Shift-3
指定区域截图：Command-Shift-4
指定程序窗口截图：Commnad-Shift-4-Space

使用快捷键 Command-Shift-4 并用鼠标选取需要截图的区域后，不要松开鼠标，然后你有几种选择：  
1. 按住鼠标的同时，按空格键，这时你能通过移动鼠标来移动整个已选择区域。   
2. 按住鼠标的同时，按 Shift 键，这时你能通过横向或竖向移动鼠标来改变已选择区域的长或高。  
3. 按住鼠标的同时，按 Option 键，这时你通过移动鼠标可以改变已选择区域的半径大小。   
4. 当你使用快捷键后发现想停止截图，在按住鼠标的同时只要按Esc就会中止截图的过程，不会保存任何文件。   
如果不改变设置, 所有截图会默认保存到桌面，也就是 Desktop 文件夹。  

常用的快捷键：http://www.douban.com/group/topic/27163791/
## office套件

为了更好的的兼容windows中的word，我目前装的是mac word2011,你也可以安装iwork或者使用开源的open office。

## Google Chrome浏览器
虽然用自带的浏览器Safari已经够用，但我还是多装了一个Chrome浏览器。

## Evernote
必备。笔记本工具，不必多说了。但我目前使用的时候经常抛异常出来，不知道是否是兼容性或稳定性做得还不够好！

## Xcode
开发必备！可以从app store免费下载,先安装 XCode, 然后安装 Command Line Tools。

## MacPorts或Homebrew(建议)
### MacPorts
MacPorts就像apt-get、yum一样，可以快速安装些软件。
你可以使用dmg或源码安装两种方式。但是我发现我的电脑在使用dmg安装一直卡在run scripts，故最后我还是使用源码安装了。
从http://distfiles.macports.org/MacPorts/MacPorts-2.1.2.tar.gz下载源码包。

```
tar zxf MacPorts-2.1.2.tar.gz
cd MacPorts-2.1.2
./configure
make && sudo make install
```

更多的使用方式，你可能参考一下这篇:

Mac OS X中MacPorts安装和使用
http://www.ccvita.com/434.html

### Homebrew
官网http://mxcl.github.com/homebrew/
The missing package manager for OS X

Install Homebrew
```
ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
```

## 版本控制工具svn客户端

如果你不习惯命令行下svn操作，你可以安装一下Versions客户端，这个有30天的试用期。  
但是要注意的是：有时候在命令行下操作更简单方便！

## jdk与eclipse(myeclipse)

如果你是搞Java开发，这应该难不倒你！

## php+mysql

相关安装配置可参考这篇文章：  
在Mac OS X中配置Apache ＋ PHP ＋ MySQL  
http://dancewithnet.com/2010/05/09/run-apache-php-mysql-in-mac-os-x/

## OmniGraffle Professional 顶级流程图软件

真的非常好用。功能强大，有很多模板。http://graffletopia.com/ 是目前最受欢迎的OmniGraffle模板站。

## 其它：QQ、迅雷等，都有相应mac版。

建议：虽然windows下的工具mac下都有相应替代产品，但熟悉命令行基本命令(如tar、find、grep、svn、sed等)的操作，会让你更加得心应手。