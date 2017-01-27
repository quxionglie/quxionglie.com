---
layout: post
title: "linux系列1-centos5.8 x64安装过程"
date: 2013-01-22 17:05
comments: true
categories: linux
---

对于linux初学者来说，鸟哥的linux私房菜是很好的学习教材了。  
我也是从linux初学者这条路走过来的,目前正在研究linux运维的技术。对于自已学过的东西，总希望留点脚印(但愿不是坑)，让大家少走点弯路。于是想写下这一系列的教程。  

<!-- more -->

建议在文本模式下安装linux，并通过命令行学习linux。   
本文的系统安装只针对Centos5.x系统，  
Centos6.x的文本安装不能自定义分区及大小，不能自定义软件包。
  
```
目录
1.	安装Centos5.8 x64  
1.1.	下载Centos,新建虚拟机	
1.2.	输入linux text ,文本模式安装	
1.3.	安装盘检验，选择skip	
1.4.	Welcome CentOS(默认OK)	
1.5.	选择语言为English		
1.6.	选择键盘布局类型为us	
1.7.	选择"自定义分区"	
1.8.	分区	
1.8.1.	设置/boot分区	
1.8.2.	设置swap分区	
1.8.3.	设置根分区	
1.8.4.	点OK确认分区	
1.9.	选择grub引导,默认OK
1.10.	boot加载参数, 默认OK
1.11.	设置GRUB密码，根据需求情况设置，这里跳过，默认OK
1.12.	确认安装位置, 默认 OK
1.13.	确认MBR安装位置, 默认 OK
1.14.	配置网络参数
1.15.	配置GATEWAY、DNS(按需求设置),我这里默认用DHCP
1.16.	设置主机名
1.17.	设置时区 Asia/Shanghai
1.18.	设置ROOT密码
1.19.	选择自定义软件包
1.20.	自定义软件包(重要)
1.21.	生成安装日志/root/install.log
1.22.	开始安装程序
1.23.	安装完成, Reboot重启
2.	配置Centos5.8
2.1.	关闭防火墙,SELinux
2.2.	设置完成,退出setup agent
3.	生产环境的分区建议
4. 问题
5. 更多资料
```

#1. 安装Centos5.8 x64
##1.1.	下载Centos,新建虚拟机
我使用的是VMware Workstation，注意选择的是64bit版本。
虚拟硬盘大小为20G。
![Alt text](/images/2013/install-centos/1_1.JPG)

##1.2. 输入linux text ,文本模式安装
![Alt text](/images/2013/install-centos/1_2.JPG)


##1.3.	安装盘检验，选择skip
##1.4.	Welcome CentOS(默认OK)
##1.5.	选择语言为English
##1.6.	选择键盘布局类型为us
##1.7. 选择"自定义分区"
![Alt text](/images/2013/install-centos/1_7.JPG)

##1.8.	分区
规划：	
/boot : 128M	
/swap : 物理内存大小*1.5,这里暂设为:512M	
/ : 剩余所有空间	
![Alt text](/images/2013/install-centos/1_8.JPG)


###1.8.1. 设置/boot分区
挂载点/boot 	
文件系统：ext3 	
size：200M 强制为主分区	
![Alt text](/images/2013/install-centos/1_8_1.JPG)


###1.8.2.	设置swap分区
文件系统：swap 	
size：512M （一般为物理内存的1.5倍）	
![Alt text](/images/2013/install-centos/1_8_2.JPG)


###1.8.3.	设置根分区
挂载点/ 	
文件系统：ext3 	
size：剩余空间	
强制为主分区  
![Alt text](/images/2013/install-centos/1_8_3.JPG)


###1.8.4.	点OK确认分区
![Alt text](/images/2013/install-centos/1_8_4.JPG)

##1.9. 选择grub引导,默认OK
##1.10.	boot加载参数, 默认OK
##1.11.	设置GRUB密码，根据需求情况设置，这里跳过，默认OK

##1.12. 确认安装位置, 默认 OK
![Alt text](/images/2013/install-centos/1_12.JPG)


##1.13.	确认MBR安装位置, 默认 OK

##1.14.配置网络参数
![Alt text](/images/2013/install-centos/1_14_0.JPG)

![Alt text](/images/2013/install-centos/1_14.JPG)




##1.15. 配置GATEWAY、DNS(按需求设置),我这里默认用DHCP
![Alt text](/images/2013/install-centos/1_15.JPG)


##1.16.	设置主机名
![Alt text](/images/2013/install-centos/1_16.JPG)


##1.17.	设置时区 Asia/Shanghai
##1.18.	设置ROOT密码
![Alt text](/images/2013/install-centos/1_18.JPG)


##1.19.	选择自定义软件包

![Alt text](/images/2013/install-centos/1_19.JPG)


##1.20. 自定义软件包

只安装以下软件包

```
base 
editors 
development librarys 
development tools 
x software development 
system tools
```

##1.21.	生成安装日志/root/install.log
##1.22.	开始安装程序
![Alt text](/images/2013/install-centos/1_22.JPG)


##1.23.	安装完成, Reboot重启

#2. 配置Centos5.8

可以在命令行输入：setup,进行下面参数的设置
##2.1.	关闭防火墙,SELinux
![Alt text](/images/2013/install-centos/2_1_0.JPG)

![Alt text](/images/2013/install-centos/2_1_1.JPG)


##2.2.	设置完成,退出setup agent


#3. 生产环境的分区建议
<table style="width: 100%;" border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td style="width: 20%;">
                服务器角色
            </td>
            <td style="width: 25%;">
                分区建议
            </td>
            <td style="width: 30%;">
                优点
            </td>
            <td style="width: 35%;">
                RAID 方案
            </td>
        </tr>
        <tr>
            <td>
                单机服务器 lamp/lnmp。如8G内存，300G硬盘
            </td>
            <td>
      /boot 100-200M swap&nbsp;16G，内存大小8G*2 /&nbsp;80G /data 180G（web及db数据）
            </td>
            <td>
                数据盘和系统盘分开，有利于出问题时维护。
            </td>
            <td>
                视数据及性能要求，可采用单盘或3块盘raid5折中。双盘raid1也可。
            </td>
        </tr>
        <tr>
            <td>
                最前端L4负载均衡器（如LVS等）
            </td>
            <td>
                /boot 100-200M swap&nbsp;内存的1-2倍 /&nbsp;
            </td>
            <td>
                简单方便，只做转发，本地数据量很少。
            </td>
            <td>
                数据量小，重要性高，可采用双盘RAID1,多一块盘降低宕机维护成本。
            </td>
        </tr>
        <tr>
            <td>
                负载均衡下的RS server,即普通节点服务器
            </td>
            <td>
                /boot 100-200M swap&nbsp;内存的1-2倍 /&nbsp;
            </td>
            <td>
                简单方便，因为有多机，对数据要求低。
            </td>
            <td>
                数据量大，重要性不高，有性能要求，数据要求低，可采用双盘RAID0
            </td>
        </tr>
        <tr>
            <td>
                数据库服务器 mysql及oracle 如16/32G内存 &nbsp; &nbsp;
            </td>
            <td>
                /boot 100-200M swap&nbsp;16G，内存的1倍 /&nbsp;100G /data&nbsp;剩余（存放db数据）
            </td>
            <td>
                数据盘和系统盘分开，有利于出问题时维护,及保持数据完整。
            </td>
            <td>
                视数据及性能要求主库可采取raid10/raid5，从库可采用raid0或raid5提高性能（读写分离的情况下。）
            </td>
        </tr>
        <tr>
            <td>
                线下备份存储服务器
            </td>
            <td>
                /boot 100-200M swap&nbsp;内存的1-2倍 /&nbsp;100G /data(存放数据)
            </td>
            <td>
                此服务器不要分区太多。只做备份，性能要求低。容量要大。
            </td>
            <td>
                可采取sata盘，raid5(多组),容量大，性能要求不高。数据有要求。
            </td>
        </tr>
        <tr>
            <td>
                在线共享存储服务器（如NFS）
            </td>
            <td>
                /boot 100-200M swap&nbsp;内存的1-2倍 /&nbsp;100G /data(存放数据)
            </td>
            <td>
                此服务器不要分区太多。NFS共享比存储多的要求就是性能要求。
            </td>
            <td>
                视性能及访问要求可以raid5,raid10,甚至raid0（要有高可用或双写方案）
            </td>
        </tr>
        <tr>
            <td>
                监控服务器 cacti,nagios
            </td>
            <td>
                /boot 100-200M swap&nbsp;内存的1-2倍 /&nbsp;
            </td>
            <td>
                重要性一般，数据要求也一般。
            </td>
            <td>
                单盘或双盘raid1即可。三盘就RAID5，看容量要求加盘即可。
            </td>
        </tr>
    </tbody>
</table>


#4. 问题
(1)硬盘主分区和逻辑分区的区别?  
(2)什么是RAID?如何做RAID?	
(3)DELL R710新服务器多硬盘Raid5后容量大于2TB如何分区  
(4)添加新硬盘后，如何分区和格式化?fdick parted	
(5)如果要安装几十上百台服务器如何实现批量无人值守安装	
后面我会单独写一篇博客介绍  	
(6)...  

#5.更多资料
(1)生产场景不同角色linux服务器分区案例分享  
http://oldboy.blog.51cto.com/2561410/634725  

(2)运维老鸟谈生产场景如何对linux系统进行分区？  
http://oldboy.blog.51cto.com/2561410/629558