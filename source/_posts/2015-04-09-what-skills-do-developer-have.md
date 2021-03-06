---
layout: post
title: "开发人员需要的技能"
date: 2015-04-09 09:50
categories: 开发
---

这是我在2012年5月写下的总结性的文章。昨晚我重新阅读了一遍，然后调整了一些内容，但总体的思想没有变。



```
目录
 1. 概述
  2.    开发技术
   2.1. 编程素养
   (1）算法类
   (2) 提升类
   2.2. 编程语言
    2.2.1.  Java（或c、c++、c# 等）
    2.2.2.  脚本语言python(或ruby等)
     (1) 为什么要学脚本语言？
     (2) 亲身经历
   2.3. 集成开发环境IDE [不细谈]
   2.4. 构建工具
   2.5. 开发最佳实践(具体内容可参考我3月份写的"应用开发部署实践.doc")
    2.5.1.  url怎样才是友好的?
   2.5.2.   调用第三方接口应该注意什么？
   2.5.3.   图片处理（压缩，水印等）
   2.5.4.   测试、设计模式等等
 3. 数据库技术[不细谈]
 4. 其它??（上面提到的都不是本文所讲内容的关键）
  4.1.  网站架构变迁路线
   4.1.1.   单机模式
   4.1.2.   数据库分离
   4.1.3.   数据库主从复制与读写分离
   4.1.4.   架设应用服务器集群
   4.1.5.   数据库分库分表
   4.1.6.   NoSQL？其它？
  4.2.  架构变迁带来的问题
    (1) 你如何知道系统的瓶颈在哪？
   (2)  你如何架设这些服务器？
   (3)  你如何监控这些服务器？
   (4)  集群化，对开发的影响？存储、缓存、session等
   (5)  数据库扩展(主从复制和读写分离,分库,分表)的过程中,你要做什么？
  4.3.  日常工作中的问题
  4.4.  问题的实质是什么？
 5. 如何写出高性能的系统?
 6. 结论：开发者需要什么技能？
 7. 知道结论后，我做了什么？
 8. 参考资料
```

<!-- more -->

#1.	概述

从去年(2011)开始，我就陆续学习jvm调优、数据库调优等知识,但到现在(2012年5月)也只是懂一点皮毛。

今天春节过来后，开始关注集群与负载均衡方面的内容。2月份北京出差回来后，总结了URL友好性、jvm优化、web组件优化、集群与负载均衡等方面的内容，写了一份《应用开发部署实践》的文档，但许多内容还在完善中。

这期间看了很多人的博客，对照自身的工作经历，不得不让人有感而发，是要总结点东西才行，于是写下了这篇文章。

从技术上面说，开发人员需要什么技能？可能每个人的看法都不一样。这里我只写了我个人当前的一些想法，想法对与错? 还需要自己思考辨别，或用时间来证明吧！

```
开发人员需要的技能
(1)编程语言是基础，必须要掌握(怎么样才能称为掌握呢？)。
(2)工作中的最佳实践你是需要了解的。但要明确的是，标准总是在不断变化的，最佳实践可能也会过时，不要让最佳实践阻止了我们持续改进。
(3)数据库知识(备份/还原,sql语句等)要了解吧。
```

但仅有这些知识就足够了吗？
开发完成了，要上线！上线就要考虑服务器的架设和优化；

程序或服务不能提供正常的服务了，我们也希望立即得到反馈的吧！

对一些高并发的应用，你要时时监控服务器(集群)的资源(CPU、IO、内存等的…).

而且你只有真正去运维自己的系统，你才知道怎么在程序里写日志，做监控，做统计…

这就是本文所讲内容的重点。

本文的主要脉络如下：

(1)	先简要的说明了开发技术和数据库技术。开发技术以java为例，说明了要学习脚本语言(python)、总结开发最佳实践等。但像类似lucune等的技术其实也很重要，但在这里尚未提及。

(2)	接着以网站架构的变迁路线和自己的工作中的问题为例，说明了开发人员其实还要学习运维。

(3)	知道结论后，我采取什么行动!

<!-- more -->

# 2.	开发技术
## 2.1.	编程素养
###(1）算法篇

```
《算法导论》
《算法（第4版）》 塞奇威克 (Robert Sedgewick) / 韦恩 (Kevin Wayne)
leetcode算法题
```

coursera上的一些算法课也不错！

如果你不满足于信息管理系统的CRUD操作，这块肯定要拿下的。

###(2) 提升篇

```
《程序员修炼之道》
《黑客与画家》
《Unix编程艺术》
《代码大全》
《Effective Java》
《clean code》
《编写可读代码的艺术》
《编程之美》
《构建之法——现代软件工程》邹欣
《持续交付:发布可靠软件的系统方法》
```

## 2.2.	编程语言
### 2.2.1.	Java（或c、c++、c#等）

真正的读好下面几本书，java基础应该很扎实了。

```
《Java编程思想》
《Java并发编程实战》
《Effective Java》
《分布式java应用 基础与实践》
```

事实上JVM的内存模型理应是Java程序员的基础知识。

```
《深入理解Java虚拟机:JVM高级特性与最佳实践(第2版)》
《Java虚拟机规范》
```

如果涉及到其它方面(如web开发，网络编程)，当然还要学很多。

```
《TCP/IP详解 卷1：协议》
...
```

基本的应用框架(如Spring/MyBatis、Shiro)及类库(如guava,commons包)肯定要熟悉的, 至少要明白实现的原理，如果能深入研究源码肯定是最好的。


### 2.2.2.	脚本语言python(或ruby等)

#### (1) 为什么要学脚本语言？
为什么要学脚本语言，因为他们实在是太方便了，很多时候我们需要写点小工具或是脚本来帮我们解决问题，你就会发现正规的编程语言太难用了。
	脚本语言学习简单上手快速，可以让你摆脱对底层语言的恐惧感，可以让你很快开发出能用得上的小程序。例如：

* 处理文本文件，如csv文件，系统产生的log文件等
* 遍历本地文件系统 (sys, os, path), 例如写一个程序统计一个目录下所有文件大小并按各种条件排序并保存结果。
* 方便与数据库打交道。
* 一些测试数据，用脚本语言非常容易生成。

我为什么要推荐python呢？大家可以看一下Eric Raymond的Why Python?
我简单总结python的几个优点：

* 它很简单易读;
* 它接近自然语言;
* 第三方库成熟、适用面广;

学习参考书：
《Python核心编程（第二版）》 （美）丘恩（Chun，W.J.）


#### (2) 亲身经历

下面的例子都是我用脚本语言(perl/python)写的。

(1)	生成测试数据(建行团经常用) 

生成随机数并保存到文件

```python
#!/usr/bin/python2.7 
# -*- coding: utf-8 -*- 
import sys 
import random 
 
''' 
生成随机数并保存到文件（可根据已生成的随机数文件生成） 
genRand.py   minValue maxValue number bit rand_file  
python genRand.py 0 100 10 5 rand_file 
@args     minValue  值范围最小值 0 
@args     maxValue  值范围最小值 1000 
@args     number 个数 
@args     bit 位数 
@args     rand_file 已生成的随机数文件(每行有一个数字串) 
''' 
 
s = set() 
 
#格式化数字 
def format(v, bit): 
    format_str = '%0'+str(bit)+'d' 
    return format_str % v 
 
#生成随机数 
def  rand(minValue, maxValue, bit) : 
    randint = random.randint(minValue, maxValue) 
    v = format(randint, bit) 
    while  v in s :  
        randint = random.randint(minValue, maxValue) 
        v = format(randint, bit) 
    return v 
     
#生成所有随机数 
def  randAll(minValue, maxValue, number , bit): 
    i = 0 
    rand_list = [] 
    while i < number : 
        i += 1 
        v = rand(minValue, maxValue , bit) 
        s.add(v) 
        rand_list.append(v) 
    return rand_list 
 
 
#从文件中读取随机数 
def  readRandFile(rand_file): 
    f = open(rand_file) 
    lines = f.readlines() 
    for line in lines  : 
        line=line.rstrip() 
        s.add(line) 
 
#将随机数保存到文件中 
def  writeRandToFile(list): 
    fw = open("out",'w') 
    for v in list  : 
        fw.write(str(v)+'\n'); 
 
 
if __name__ == '__main__': 
    lenArgc = len(sys.argv) 
    if lenArgc >=5 :  
        minValue = int(sys.argv[1]) 
        maxValue = int(sys.argv[2]) 
        number = int(sys.argv[3]) 
        bit = int(sys.argv[4]) 
        if lenArgc > 5    : 
            rand_file = sys.argv[5]         
            readRandFile(rand_file)    #从文件中读取随机数     
 
        #生成随机数 
        rand_list = randAll(minValue, maxValue, number , bit) 
        writeRandToFile(rand_list) 
    else : 
        print "参数不正确！" 
```

(2)	读取execl(hui99的操作定义)生成Selenium脚本

execl的数据列有以下，

![01](/images/2015/0409/01.png)


用脚本去读取execl文件，获取相关数据信息，生成selenium脚本文件并执行。
不生成selenium脚本文件，生成sql文件也行，但并非每个人都能直接操作数据库的。

(3)	mp3 tag操作

电脑下了n多mp3文件，你怎么读取mp3里的tag信息，里面的很多标签都乱七八糟的,如何去修改呢?一个一个文件属性去修改？ python里有非常简单的类库。

![01](/images/2015/0409/02.png)

(4)	文件重命名

你下载了数G的电视剧/动画片,文件名又乱又长，怎么办？

我确实遇到过这种事情。

柯南都500多集，海贼王400多集、棋魂、灌篮高手等，美剧都六七季，一季20集。你就别想手动修改了，会死人的。

用脚本语言一下了就可以搞定，linux shell也可以轻松制定。

美好生活就是如此的简单。

## 2.3.	集成开发环境IDE [不细谈]

## 2.4.	构建工具

使用自动构建工具的理由：

* (1)	在非图形化界面下构建程序；
* (2)	构建过程繁琐而且步骤固定，自动构建工具比交互式的手动构建省事省心；
* (3)	多人分别为同一个项目工作，需要有统一和一致的构建环境，比如类库依赖。

ant/maven在几年前还不错，到了现在(2015年)已经略显沉重。我这里推荐gradle。我从13年就开始用gradle,上手简单，值得拥有。
 
## 2.5.	开发最佳实践(具体内容可参考我3月份写的"应用开发部署实践.doc")
### 2.5.1.	url怎样才是友好的?

阿烈：为什么我会提到url的问题？

产品运营天天跟你闹：URL太长了，URL参数太多了，URL太乱了，这样搜索引擎搜不到我们的产品啊？你要怎么办？

![01](/images/2015/0409/03.png)

###2.5.2.	调用第三方接口应该注意什么？

![01](/images/2015/0409/04.png)

###2.5.3.	图片处理（压缩，水印等）

阿烈：为什么hui99的图片处理这么烂？

压缩的效果太差了!

![01](/images/2015/0409/05.png)

###2.5.4.	测试、设计模式等等

#3.	数据库技术[不细谈]

能有dba级别肯定牛，但这种人可能会比较少。

但也至少熟练应用mysql(或其它关系数据库), 知道SQL优化。

#4.	其它??（上面提到的都不是本文所讲内容的关键）

我们从一个网站从小到大引起的架构变迁，来看待这个问题！

这里以豆瓣为例。具体内容请点击[豆瓣网技术架构变迁](http://www.infoq.com/cn/presentations/hongqn-douban)。

##4.1.	网站架构变迁路线

###4.1.1.	单机模式
这一阶段没有太大的访问量，甚至只有一台服务器就搞定了所有的访问。压力不高。
应用服务器和数据库放在同一台机子上，前端可能还会架设web服务器(IIS/apache/nginx等)，实现动静分离。

![01](/images/2015/0409/41.png)

![01](/images/2015/0409/42.png)


###4.1.2.	数据库分离
压力增大，出现瓶颈，开始分离数据库。

![01](/images/2015/0409/43.png)

![01](/images/2015/0409/44.png)

![01](/images/2015/0409/45.png)


###4.1.3.	数据库主从复制与读写分离
网站初具规模，DB压力大增，单独的一台DB已经满足不了现在的访问量，开始考虑读写分离的Master-slave库，使用三个及以上的服务器。

访问量继续增加，增加到了DB的压力在Master的机器上非常的明显了，Master开始出现吃不消的情况，出现写耗尽。主从也已经不能满足要求，需要进一步解决负载问题，此时要引入Mysql Proxy程序，进行中间层代理，实现负载均衡，易于扩展。

注：读写分离采用的方法是写数据库时在一个数据库上写入，而要读取时则从多个其他的数据库中读取，通常将用于写入的库称为master库，用于读取的库为slave库。读写分离适用于读多写少，并允许一定延时的业务中。

![01](/images/2015/0409/46.png)

![01](/images/2015/0409/47.png)

![01](/images/2015/0409/48.png)

###4.1.4.	架设应用服务器集群
随着访问数的增长，单台应用服务器已经不能响应如此多的请求，必须要架设应用服务器集群了。

![01](/images/2015/0409/49.png)

![01](/images/2015/0409/50.png)

![01](/images/2015/0409/51.png)

###4.1.5.	数据库分库分表

在系统发展的初期，通常会将各种不同的数据库放在同一个数据库中，随着业务的多元化及业务的水平伸缩，数据库的连接数会成为稀缺和资源，对于这种情况分库是个不错的选择。

分库通常按照业务领域将原来存储在同一个数据库的数据拆分到多个数据库中。例如ebay就按照其业务领域分为商品、用户、评价、交易等数据库，每个数据库只用于处理相关业务的数据，因此可用的数据库连接数会得到很大提升。

分表意味着我们可以将同一数据表中的记录通过特定的算法进行分离，分别保存在不同的数据库表中，从而可以部署在不同的数据库服务器上。

![01](/images/2015/0409/53.png)

![01](/images/2015/0409/54.png)

![01](/images/2015/0409/55.png)


###4.1.6.	NoSQL？其它？
##4.2.	架构变迁带来的问题
####(1)	你如何知道系统的瓶颈在哪？
大家也看得出来，访问量的不断增大导致了架构的不断变迁。访问量的增大导致了系统出现瓶颈，而不能满足用户的请求了。

通常瓶颈的表象是资源消耗过多、外部处理系统的性能不足，或者资源消耗不多，但程序的响应速度却仍达不到要求(建行团有一次秒杀时遇到到这种问题)。

资源主要消耗在CPU、文件IO、网络IO以及内存方面，机器有资源是有限的，当某资源消耗过多时，通常会造成系统的响应速度慢。

你怎么知道系统出现了瓶颈了呢？出现的瓶颈在哪里呢？

###(2)	你如何架设这些服务器？

###(3)	你如何监控这些服务器？
你不监控，你就不知道系统的瓶颈在哪，性能优化、架构变迁也就无从谈起！

而且网络状况复杂，难以保证7×24小时100%的在线，你当然不希望自己网站或者服务器由于各种原因而不能访问。

故障的原因主要：

* 网站被挂马。网站一旦有漏洞，被挂马是常有的事。
* 网站代码出错、数据库出错、网站占CPU太高、空间占满、空间过期。
* 域名解析出错、域名过期。
* 网站或者服务器由于有害信息、备案等原因被关或封IP。
* 网站或者服务器被攻击。同台服务器网站或者网段被攻击也殃及池鱼。
* 服务器出现故障或者需要维护。
* 网络运营商线路出现故障，特别是南北互通。

很多故障是不定时、不可预料的。最勤快的站长网管都不可能24小时去刷新网页，去Ping服务器。这自然需要自动的监控服务。

###(4)	集群化，对开发的影响？存储、缓存、session等
###(5)	数据库扩展(主从复制和读写分离,分库,分表)的过程中,你要做什么？

##4.3.	日常工作中的问题

也许大家会说，我们的网站的访问量这么少，还没有必要考虑上面的问题。
好，我列出工作中一些问题:

```
(1)	为什么我们的服务器都是windows机器，而不是linux
(2)	hui99存储空间不足，导致hui99邮件服务器挂掉。
(3)	北京农商行,手机银行服务端连接到网银服务器，为什么自己写负载均衡策略？
(4)	为什么建行团，迁移机房后，远程登录用户不用administrator了。
(5)	21的数据服务器僵死，导致hui99的tomcat线程到达200,而不能提供正常服务！(2012-03-25)
(6)	建行团有一个200M的日志文件，你如何查看其内容？如果是2G的呢?(一般的文件编辑器打不开这么大的)
(7)	建行团秒杀过程中，如何监控CPU，内存，磁盘IO等？
(8)	如何提供7*24*365的服务(项目更新必须停机重起，服务会暂停)?
(9) ...
```
##4.4.	问题的实质是什么？

服务器（集群）架设。

服务器的监控。

服务器的优化。

服务器的安全。

。。。

实现这些，你还需要掌握运维的知识。

那什么是运维呢？
运维关键技术点：

*	大量高并发网站的设计方案 ；
*	高可靠、高可伸缩性网络架构设计；
*	网站安全问题，如何避免被黑？
*	南北互联问题,动态CDN解决方案；
*	海量数据存储架构;
*	等等...

#5.	如何写出高性能的系统?
从当当网上搜"高性能"，你可以看到下面列出来的图书：

```
《高性能Linux服务器构建实战:运维监控、性能调优与集群应用》
《高性能网站建设指南》
《高性能MYSQL影印版》
《构建高性能Web站点》
《实战Nginx：取代Apache的高性能Web服务器》
………
```

一个高性能的系统，不仅涉及到开发的程序代码，也与服务器、数据库、web服务器等息息相关。

罗马不是一日建成的，高性能系统不是一下子就能实现的？高性能系统是PDCA（Plan Do Check Act）持续改善的过程，是开发、上线、监控、优化反复循环的过程。

#6.	结论：开发者需要什么技能？

(1)	开发技术

(2)	数据库技术

(3)	运维技术

技术之间是相辅相成的。

#7.	知道结论后，我做了什么？

(1)	学习linux

(2)	参加运维培训班

(3)	学习python

(4)	多思考、总结、实践

#8.	参考资料
(1)	Java应用运维 by bluedavy 

<http://blog.bluedavy.com/?p=363>

(2)	我们需要专职的QA吗？ by 陈皓

<http://coolshell.cn/articles/6994.html>

(3)	程序员技术练级攻略 by 陈皓

<http://coolshell.cn/articles/4990.html>

(4)	Why Python? By Eric Raymond

<http://www.linuxjournal.com/article/3882>

Eric Raymond的Why Python?的中文翻译

<http://blog.donews.com/ygao/archive/2005/10/25/602051.aspx>

(5)	程序员需要具备的基本技能 by 陈皓

<http://coolshell.cn/articles/428.html>

-----

初稿写于2012年5月，2015年4月9日修改了部分内容。

(全书完)