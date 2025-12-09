---
title: 《Linux学习笔记》集合
date: 2023-12-27 17:19:12
comments: true
categories: linux
---

点击 [Linux学习笔记](https://www.quxionglie.com/learn-linux-notes/) 查看在线版本。

本书通过 mdbook 发布。

# 介绍

这是我在2012年学习 Linux 的笔记。当前仅是linux基础部分，其它如多机房运维、集群等在中级篇(未发布)。

我本身是做 java 开发的，但在当时(12年)，你会发现技术大会上面很少有讲 java 的，都是说架构、系统变迁等之类的。
所以仅是学好 java 够了吗？他依赖的数据库，运行的环境，集群、架构等需不需要我们去学习。

如果多年以后再往回看，go/rust也兴起来了。语言很重要，但不应该成为决定性的因素。千万不要给自己设置上限，例如我是java开发，我就只学java，不学其它。

像有些知识，如LAMP、nagios、cacti等因为新技术的兴起（如容器化等），已经不用或少用，我这里就不会再列出来了。

linux 命令这块很多上参考了《鸟哥的linux私房菜》。初学者可以多看看。

其它笔记大部分是手敲的，建议大家也这么做。有时候你看起来会了，实际上却什么都不懂，动手才会加深理解和记忆。

当时笔记 linux 环境是基于 CentOS 5 的，现在 CentOS 已经快不维护了。如果不特别说明当前环境都是基于 CentOS7.9的。

初学者进入一个领域，往往陷入细节 或 抓不住重点。久了人就会疲惫和迷茫，最后放弃( xxx 从入门到放弃)。

我的建议是：找人带。找培训班(靠谱的)或网上课程(如极客时间)，不要自己一个人闷头学，这样效率很低。

找人带的好处是：相当于你站在别人的肩膀上，起点比较高。

如果你是天才就另说。

植一棵树最好的时间是十年前，其次是现在。

# 目录
```
介绍
1. Linux安装
1.1. CentOS7.9系统安装文档
1.2. linux系统的开机启动过程
1.3. 命令总结(cat pwd ls rm mkdir touch head tail ln chkconfig)
1.4. linux下常用的命令快捷键

2. Linux服务器优化
2.1. 配置优化Centos5.X Linux操作系统
2.2. 命令总结(find、wc、tar、cut、grep、egrep、date、which、echo、shutdown、reboot)
2.3. linux shell中单引号、双引号及不加引号的简单区别
2.4. linux下软链接和硬链接的区别

3. 文件与目录
3.1. Linux文件和目录权限实战讲解
3.2. 系统目录结构及重要路径
3.3. linux文件类型总结
3.4. Linux文件和目录的属性及权限
3.5. 命令总结(stat、sed、awk)
3.6. 命令总结-文件查看与处理命令(more、less、tree、chattr、lsattr)
3.7. 命令总结-用户管理命令-用户(useradd、userdel、passwd、chage)
3.8. 命令总结-用户管理命令-用户组(groupadd、groupdel)
3.9. 命令总结-查看系统用户登录信息命令-(w、who、users、last)

4. crontab
4.1. Linux系统定时任务详解
4.2. Linux系统的用户和用户组管理
4.3. Linux用户切换(su sudo)
4.4. 命令总结-磁盘空间的命令(mount umount)
4.5. 命令总结-附加命令(nohup watch time ps)

5. 硬盘
5.1. 硬盘的基础知识介绍
5.2. 硬盘的工作原理介绍
5.3. 硬盘分区相关知识介绍
5.4. RAID技术
5.5. 命令总结-信息显示命令(uname hostname dmesg uptime du df)
5.6. 命令总结-磁盘空间的命令(fsck dd dump fdisk parted)
5.7. fdisk分区命令实战讲解
5.8. fstab详细介绍及救援模式修复fstab实战案例
5.9. parted分区命令实战讲解
5.10. linux文件系统知识介绍

6. nfs
6.1. nfs网络文件系统服务介绍与实战
6.2. SSH KEY免密码验证分发、管理、备份实战讲解
6.3. 命令总结-基本网络操作命令(telnet ssh scp wget ping route ifconfig ifup ifdown netstat)

7. rsync
7.1. Rsync生产场景实战应用指南
7.2. 深入网络操作命令(mail mutt nslookup dig traceroute df du fsck dd)

8. MySQL基础
8.1. MySQL常用基础命令操作实战讲解
8.2. MySQL单实例的安装配置指南
8.3. MySQL主从复制原理详解
8.4. MySQL主从复制实践讲解
8.5. MySQL数据库安全权限控制管理思想
8.6. MySQL灾难恢复实战多案例精华讲解
8.7. mysqlbinlog
8.8. mysqldump

9. shell
9.1. shell脚本编程精讲
9.2. linux下shell脚本开发规范与制度

10. Lvs+keepalived集群
10.1. Lvs负载均衡及Keepalived高可用技术应用详解
10.2. Lvs+keepalived集群生产实战模拟
10.3. Lvs+keepalived集群生产实战维护要点
11. iptables
11.1. iptables防火墙应用指南
```

end.
