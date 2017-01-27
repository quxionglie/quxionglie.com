---
layout: post
title: "JAVA垃圾回收入门"
date: 2015-03-21 21:04
comments: true
categories: jvm
---

2012年写的笔记。

[点击下载pdf](http://pan.baidu.com/s/1jG03A8A)

<!-- more -->


文档目录

```
目录	1
1.	java编译执行	3
2.	为什么有垃圾回收	3
3.	java内存区域	4
3.1.	java虚拟机运行时数据区	4
4.	JVM内存参数	5
4.1.	–server与-client	5
4.2.	设置最大堆内存-Xmx<max>，最小堆内存-Xms<min>	6
4.3.	设置新生代-Xmn<size>	6
4.4.	设置持久代-XX:MaxPermSize=<N>, -XX:PermSize=<N>	6
4.5.	设置线程栈-Xss<size>	6
4.6.	设置新生代eden与survivor的比例-XX:SurvivorRatio	7
4.7.	堆分配参数总结	7
5.	java程序运行时常见错误	8
5.1.	java.lang.OutOfMemoryError: Java heap space	8
5.2.	java.lang.OutOfMemoryError: PermGen space	9
5.3.	java.lang.OutOfMemoryError: unable to create new native thread	9
5.4.	java.lang.StackOverflowError	10
6.	tomcat与resin中设置JVM参数	10
6.1.	tomcat(tomcat-6.0.36)	10
6.2.	resin(resin-3.1.12)	10
7.	如何进行垃圾回收	11
8.	哪些对象需要回收-发现垃圾对象	11
8.1.	引用计数算法	11
8.2.	根搜索算法	12
9.	什么时候回收垃圾对象	13
9.1.	分代	13
9.1.1.	新生代(Young Generation)	13
9.1.2.	旧生代（Old Generation/Tenuring Generation）	13
9.1.3.	持久代（Permanent Generation）	14
9.2.	分代GC的类型	14
9.2.1.	Minor GC	14
9.2.2.	Full GC	14
9.3.	GC的步骤	15
10.	如何回收垃圾对象	15
10.1.	垃圾收集算法	15
10.1.1.	标记-清除算法(Mark-Sweep)	15
10.1.2.	复制算法(copying)	16
10.1.3.	标记-压缩算法(Mark-Compact)	17
10.1.4.	分代收集算法(Generation Collection)	18
10.2.	垃圾收集器	18
10.2.1.	串行收集器	19
10.2.2.	并行收集器	20
10.2.3.	并发收集器(CMS)	22
10.2.4.	Garbage First(G1)收集器-略	22
10.2.5.	选择收集器(Selecting a Collector)	23
10.2.6.	总结	24
11.	JVM监控与分析	26
11.1.	输出GC日志	27
11.1.1.	使用举例	27
11.2.	jps 虚拟机进程状态工具	31
11.3.	jstat 统计信息监控工具	31
11.4.	jinfo java配置信息工具	32
11.5.	jmap 内存映像工具	33
11.5.1.	使用举例	34
11.6.	jconsole	36
11.7.	VisualVM 多合一故障处理工具	38
12.	JVM调优过程	39
12.1.	测试小例子	40
12.1.1.	测试环境	40
12.1.2.	测试场景1：默认tomcat配置	41
12.1.3.	测试场景2：JVM配置：-Xmx512M -Xms512M -Xmn128M -XX:+UseSerialGC	43
12.1.4.	测试场景3：JVM配置：-Xmx512M -Xms512M -Xmn128M -XX:+UseParallelGC -XX:+UseParallelOldGC	44
12.1.5.	测试结果分析	45
13.	参考资料	46
14.	附录	46
14.1.	Server-Class Machine Detection	46
14.2.	Garbage Collector Ergonomics	47
14.3.	Should I use a 32- or a 64-bit JVM?	49
14.4.	Increasing heap size – beware of the Cobra Effect	53
```
