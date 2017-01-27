---
layout: post
title: "linux系列2 - pxe+dhcp+nfs+kickstart无人值守批量安装Centos5.8 x64"
date: 2013-01-24 18:05
comments: true
categories: linux
---

上文进到，如果我们要安装几十上百台服务器，如何做呢？总不能一台一台人工安装吧！
本文就来解决这个问题，看一下如何实现批量无人值守安装centos5.8 x64。

<!-- more -->

```
目录 
1.	原理介绍	
1.1.	什么是PXE	
1.2.	什么是KickStart	
1.3.	无人值守安装原理	
1.4.	无人值守安装过程详细说明	
1.5.	PXE+kickStart安装条件	
2.	安装环境	
2.1.	Kickstart服务器环境说明	
3.	一键安装脚本	
4.	安装说明	
4.1.	挂载系统安装盘	
4.2.	设置nfs	8
4.3.	设置tftp	
4.4.	配置DHCP	
4.5.	配置ks.cfg文件	
5. OS安装过程
```

#1. 原理介绍
##1.1.	什么是PXE
严格来说，PXE并不是一种安装方式，而是一种引导方式。进行PXE安装的必要条件是在要安装的计算机中必须包含一个PXE支持的网卡(NIC)，即网卡中必须要有PXE Client。PXE(Pre-boot Execution Environment)协议可以使计算机通过网络启动。此协议分为Client端和Server端，而PXE Client则在网卡的ROM中。当计算机引导时，BIOS把PXE Client调入内存中执行，然后由PXE Client将放置在远端的文件通过网络下载到本地运行。运行PXE协议需要设置DHCP服务器和TFTP服务器。DHCP服务器会给PXE Client(将要安装系统的主机)分配一个IP地址，由于是给PXE Client分配IP地址，所以在配置DHCP服务器时需要增加相应的PXE设置。此外，在PXE Client的ROM中，已经存在了TFTP Client，那么它就可以通过TFTP协议到TFTP Server上下载所需的文件了。
##1.2.	什么是KickStart
KickStart是一种无人值守的安装方式。它的工作原理是在安装过程中记录典型的需要人工干预填写的各种参数，并生成一个名为ks.cfg的文件。如果在安装过程中(不只局限于生成KickStart安装文件的机器)出现要填写参数的情况，安装程序首先会去查找KickStart生成的文件，如果找到合适的参数，就采用所找到的参数；如果没有找到合适的参数，便需要安装者手工干预了。所以，如果KickStart文件涵盖了安装过程中可能出现的所有需要填写的参数，那么安装者完全可以只告诉安装程序从何处取ks.cfg文件，然后就去忙自己的事情。等安装完毕，安装程序会根据ks.cfg中的设置重启系统，并结束安装。

1.1、1.2资料来源：  
《构建高可用Linux服务器（第2版）》   
book.51cto.com/art/201208/354168.htm

##1.3. 无人值守安装原理
客户机通过支持pxe的网卡向网络中发送请求DHCP信息的广播，请求获取ip地址等信息，网络中的DHCP服务器收到请求验证通过后，给客户机返回ip地址和相关信息(TFTP服务器地址、启动文件等),之后，客户机向网络中的TFTP服务器请求并下载操作系统安装所需要的文件。在这个过程中需要一台服务器来提供启动文件、安装文件、安装过程中的自动应答文件。



##1.4.	无人值守安装过程详细说明
![alt txt](/images/2013/pxe-dhcp-nfs-kickstart/01.jpg)


(1)	PXE client向DHCP server发送请求   
将支持PXE网络接口卡(NIC)的计算机的BIOS设置为以PXE方式启动，此时，PXE Client通过PXE Boot ROM会以UDP(简单用户数据协议)的形式在网络中发送一个广播请求，请求DHCP服务器分配IP地址等相关信息。

(2)	DHCP server应答PXE client  
DHCP Server收到客户端请求，验证是否来自合法的PXE Client请求，验证通过后，它会回应PXE Client，回应中包含了为PXE Client分配的IP地址、pxelinux启动程序(TFTP)的位置，以及配置文件所在位置。

(3)	PXE client请求下载启动文件  
客户端收到服务器的回应后，会向TFTP服务器请求传送启动系统安装所需文件。这些启动文件包括：pxelinux.0、pxelinux.cfg/default、vmlinuz、initrd.img等文件。

(4)	TFTP服务器响应客户端请求并传送文件  
当TFTP服务器收到客户端请求后，它们之间会有更多的信息在客户端和服务器之间做应答，用以决定启动参数。BootROM由TFTP通讯协议从Boot Server下载启动安装程序所必须的文件（pxelinux.0、pxelinux.cfg/default）。default文件下载完成后，会根据该文件中定义的引导顺序，启动linux安装程序的引导内核。

(5)	请求下载自动应答文件   
pxe client通过pxelinux.cfg/default文件成功引导linux安装内核后，安装程序首先确定你通过什么安装来安装linux,如果是通过网络安装（NFS,FTP,HTTP）,则会在这个时候初始化网络，并定位安装系统所需的二进制包以及配置文件的位置。接着会读取该文件中指定的自动应答文件ks.cfg,然后根据ks.cfg中文件位置请求下载相关文件。  

(6)	客户端安装操作系统
将ks.cfg文件下载回来后，通过该文件找到os server(可以是http,NFS,FTP等)，并按照该文件的配置请求下载安装过程中需要的软件包。   
OS server和客户端建立连接后，将开始传输软件包。客户端开始安装操作系统，安装完成后，提示重新引导计算机。  

PXE client是需要安装linux的客户机，TFTP Server、DHCP Server和NFS Server运行在另外一台或多台Linux Server上。Bootstrap文件、配置文件、Linux内核都放置在Linux Server上的TFTP服务器的根目录下。而linux的系统安装相关文件则存在NFS Server的共享目录中。   
PXE client工作过程中，需要三个二进制文件：Bootstrap、Linux内核和Linux根文件系统。Bootstrap文件是可执行程序，它向用户提供简单的控制界面，并根据用户的选择，下载合适的Linux内核及根文件系统。   

1.3,1.4资料感谢老男孩老师#
  
##1.5. PXE+kickStart安装条件  
(1)	DHCP 服务器  
(2)	TFTP 服务器	
(3)	KickStart所生成的ks.cfg配置文件	
(4)	一台存放系统安装文件的服务器，如 NFS、HTTP 或 FTP 服务器  
(5)	带有 PXE 支持网卡的待安装主机  

#2. 安装环境
##2.1.	Kickstart服务器环境说明
###操作系统：

``` bash
[root@lie-pc ~]# cat /etc/redhat-release 
CentOS release 5.8 (Final) 
[root@lie-pc ~]# uname -mi
x86_64 x86_64
#内核版本：
[root@lie-pc ~]# uname -r
2.6.18-308.el5
```

####主机网络参数设置：
主机名:		lie-pc   
网卡eth0:	10.0.0.34  
默认网关:		10.0.0.2  
子网掩码:		255.255.255.0  

#3. 一键安装脚本
auto_install_centos.sh

```bash
#!/bin/bash
 # auto install kickstart server
 # the os root's passwd is 123456
 #
 # Filename: auto_install_centos.sh
 # Author: alex
 # Date:   2012-10-17  
 # Email:  xionglie.qu@gmail.com  
 #------------Environment------------  
 #   Linux 2.6.18-308.el5  i686 or x86_64
 #   CentOS release 5.8 (Final)
 #-----------------------------------  
 
 KICKSTART_SERVER_IP=`/sbin/ifconfig eth0 | grep 'inet addr' | awk '{print $2}' | awk -F: '{print $2}'`
 KICKSTART_SERVER_NET_ID=`echo ${KICKSTART_SERVER_IP} | cut -d '.' -f1-3 `
 KICKSTART_SERVER_SUBNET=${KICKSTART_SERVER_NET_ID}.0
 KICKSTART_SERVER_NETMASK=255.255.255.0
 KICKSTART_SERVER_ROUTE=${KICKSTART_SERVER_NET_ID}.2
 KICKSTART_SERVER_DHCP_IP_START=${KICKSTART_SERVER_NET_ID}.128
 KICKSTART_SERVER_DHCP_IP_END=${KICKSTART_SERVER_NET_ID}.254
 
 
 echo "----- 1.配置nfs服务器 --------------"
 #创建共享目录
 mkdir -p /data/sys
 mount -o loop /root/CentOS-5.8-x86_64-bin-DVD-1of2.iso /data/sys
 echo "mount -o loop /root/CentOS-5.8-x86_64-bin-DVD-1of2.iso /data/sys" >>/etc/rc.local
 
 #如果是用光盘就用下面的命令
 #mount /dev/cdrom /data/sys
 #echo "mount /dev/cdrom /data/sys" >>/etc/rc.local
 ls -l /data/sys
 
 #安装nfs,分发共享目录
 rpm -qa | grep nfs
 cat > /etc/exports << EOF
 /data/sys ${KICKSTART_SERVER_SUBNET}/24(ro,sync)
 /data/kickstart ${KICKSTART_SERVER_SUBNET}/24(ro,sync)
 EOF
 cat /etc/exports
 
 #启动nfs,设置开机启动
 /etc/init.d/portmap restart
 /etc/init.d/nfs restart
 showmount -e 127.0.0.1
 
 chkconfig portmap on
 chkconfig nfs on
 chkconfig --list | egrep "nfs|portmap"
 
 echo "----- 2.配置TFTP服务器 --------------"
 yum -y install tftp-server* -y
 sed -i '/disable.*=/ s#yes#no#' /etc/xinetd.d/tftp
 /etc/init.d/xinetd restart
 chkconfig xinetd on
 
 /bin/cp /usr/lib/syslinux/pxelinux.0 /tftpboot/
 /bin/cp /data/sys/images/pxeboot/vmlinuz /tftpboot/
 /bin/cp /data/sys/images/pxeboot/initrd.img /tftpboot/
 
 mkdir /tftpboot/pxelinux.cfg -p
 /bin/cp /data/sys/isolinux/isolinux.cfg /tftpboot/pxelinux.cfg/default
 chmod a+r /tftpboot/pxelinux.cfg/default
 sed -i "s#default linux#default text#" /tftpboot/pxelinux.cfg/default
 sed -i "s#append initrd=initrd.img text#append ks=nfs:${KICKSTART_SERVER_IP}:/data/kickstart/ks.cfg ksdevice=eth0 initrd=initrd.img text#" /tftpboot/pxelinux.cfg/default
 
 echo "----- 3.配置DHCP --------------"
 yum -y install dhcp*
 #/bin/cp /usr/share/doc/dhcp-3.0.5/dhcpd.conf.sample /etc/dhcpd.conf
 
 cat > /etc/dhcpd.conf << EOF
 ddns-update-style none;
 ignore client-updates;
 allow booting;
 allow bootp;
 default-lease-time 21600;
 max-lease-time 43200;
 option routers ${KICKSTART_SERVER_ROUTE};
 
 subnet ${KICKSTART_SERVER_SUBNET} netmask ${KICKSTART_SERVER_NETMASK} {
 range dynamic-bootp ${KICKSTART_SERVER_DHCP_IP_START} ${KICKSTART_SERVER_DHCP_IP_END};
 next-server ${KICKSTART_SERVER_IP};
 filename "/data/kickstart/ks.cfg";
 next-server ${KICKSTART_SERVER_IP};
 filename "pxelinux.0";
 }
 EOF
 
 /etc/init.d/dhcpd restart
 #出错的话，cat /var/log/message
 
 chkconfig dhcpd on
 chkconfig --list dhcpd
 
 echo "----- 4.配置kickstart --------------"
 mkdir -p /data/kickstart/
 #/bin/cp /root/anaconda-ks.cfg /data/kickstart/ks.cfg
 #chmod 644 /data/kickstart/ks.cfg
 
 cat > /data/kickstart/ks.cfg << EOF 
 install
 nfs --server=${KICKSTART_SERVER_IP} --dir=/data/sys
 lang en_US.UTF-8
 keyboard us
 network --device=eth0 --bootproto=dhcp --onboot=on --hostname stu412
 #network --device=eth1 --bootproto=dhcp --onboot=on
 
 #root password
 rootpw 123456
 #rootpw --iscrypted \$1\$dQVhf2.F\$wUhtrf7gHDN8Q6EtlrWSF.
 
 
 authconfig --enableshadow --enablemd5
 selinux --disabled
 firewall --disabled
 timezone --utc Asia/Shanghai
 bootloader --location=mbr --driveorder=sda
 firstboot --disabled
 logging --level=info
 zerombr
 
 
 #clearpart --linux
 clearpart --all
 part /boot --fstype ext3 --size=200 --asprimary
 part swap --size=512 --asprimary
 part / --fstype ext3 --size=1 --grow --asprimary
 reboot
 
 %packages
 @base
 @core
 @development-libs
 @development-tools
 @editors
 @system-tools
 @x-software-development
 
 %post
 #!/bin/bash
 mkdir -p /application/tools
 mkdir -p /server/scripts
 
 EOF
 
 echo "-----done-----"

```


#4. 安装说明
##4.1.	挂载系统安装盘

```
#创建共享目录
mkdir -p /data/sys
mount -o loop /root/CentOS-5.8-x86_64-bin-DVD-1of2.iso /data/sys
echo "mount -o loop /root/CentOS-5.8-x86_64-bin-DVD-1of2.iso /data/sys" >>/etc/rc.local
 
#如果是用光盘就用下面的命令
#mount /dev/cdrom /data/sys
#echo "mount /dev/cdrom /data/sys" >>/etc/rc.local
ls -l /data/sys
```

##4.2. 设置nfs 

操作过程：

```bash
#安装nfs,分发共享目录
cat > /etc/exports << EOF
/data/sys ${KICKSTART_SERVER_SUBNET}/24(ro,sync)
/data/kickstart ${KICKSTART_SERVER_SUBNET}/24(ro,sync)
EOF
```

```
[root@lie-pc ~]# cat /etc/exports 
/data/sys 10.0.0.0/24(ro,sync)
/data/kickstart 10.0.0.0/24(ro,sync)

[root@lie-pc ~]# showmount -e localhost 
Export list for localhost: 
/data/sys 10.0.0.0/24 
/data/kickstart 10.0.0.0/24
```

##4.3. 设置tftp
操作命令：

```
 yum -y install tftp-server* -y
 sed -i '/disable.*=/ s#yes#no#' /etc/xinetd.d/tftp
 /etc/init.d/xinetd restart
 chkconfig xinetd on
 
 /bin/cp /usr/lib/syslinux/pxelinux.0 /tftpboot/
 /bin/cp /data/sys/images/pxeboot/vmlinuz /tftpboot/
 /bin/cp /data/sys/images/pxeboot/initrd.img /tftpboot/
 
 mkdir /tftpboot/pxelinux.cfg -p
 /bin/cp /data/sys/isolinux/isolinux.cfg /tftpboot/pxelinux.cfg/default
 chmod a+r /tftpboot/pxelinux.cfg/default
 sed -i "s#default linux#default text#" /tftpboot/pxelinux.cfg/default
 sed -i "s#append initrd=initrd.img text#append ks=nfs:${KICKSTART_SERVER_IP}:/data/kickstart/ks.cfg ksdevice=eth0 initrd=initrd.img text#" /tftpboot/pxelinux.cfg/default
```

```
[root@lie-pc ~]# cat /etc/xinetd.d/tftp 
# default: off
# description: The tftp server serves files using the trivial file transfer \
#	protocol. The tftp protocol is often used to boot diskless \
#	workstations, download configuration files to network-aware printers, \
#	and to start the installation process for some operating systems.
service tftp
{
  　　socket_type	 = dgram
  　　protocol	 = udp
  　　wait	 = yes
  　　user	 = root
  　　server	 = /usr/sbin/in.tftpd
  　　server_args	 = -s /tftpboot
  　　disable	 = no #将yes改为no
  　　per_source	 = 11
  　　cps	 = 100 2
     　flags	 = IPv4
}
[root@lie-pc ~]# cat /tftpboot/pxelinux.cfg/default
default text
prompt 1
timeout 600
display boot.msg
F1 boot.msg
F2 options.msg
F3 general.msg
F4 param.msg
F5 rescue.msg
label linux
　　kernel vmlinuz
　　append initrd=initrd.img
label text
　　kernel vmlinuz
　　append ks=nfs:10.0.0.34:/data/kickstart/ks.cfg ksdevice=eth0 initrd=initrd.img text
label ks
　　kernel vmlinuz
　　append ks initrd=initrd.img
label local
　　localboot 1
label memtest86
　　kernel memtest　　
　　append -
```

##4.4. 配置DHCP
操作命令：

```bash
yum -y install dhcp*
cat > /etc/dhcpd.conf << EOF
ddns-update-style none;
ignore client-updates;
allow booting;
allow bootp;
default-lease-time 21600;
max-lease-time 43200;
option routers ${KICKSTART_SERVER_ROUTE};

subnet ${KICKSTART_SERVER_SUBNET} netmask ${KICKSTART_SERVER_NETMASK} {
　　range dynamic-bootp ${KICKSTART_SERVER_DHCP_IP_START} ${KICKSTART_SERVER_DHCP_IP_END};
　　next-server ${KICKSTART_SERVER_IP};
　　filename "/data/kickstart/ks.cfg";
　　next-server ${KICKSTART_SERVER_IP};
　　filename "pxelinux.0";
}
EOF
```

```
[root@lie-pc ~]# cat /etc/dhcpd.conf
ddns-update-style none;
ignore client-updates;
allow booting;
allow bootp;
default-lease-time 21600;
max-lease-time 43200;
option routers 10.0.0.2;

subnet 10.0.0.0 netmask 255.255.255.0 {
　　range dynamic-bootp 10.0.0.128 10.0.0.254;
　　next-server 10.0.0.34;
　　filename "/data/kickstart/ks.cfg";
　　next-server 10.0.0.34;
　　filename "pxelinux.0";
}

```


##4.5. 配置ks.cfg文件

```
[root@lie-pc ~]# cat /data/kickstart/ks.cfg 
install	 #安装系统而不是升级
nfs --server=10.0.0.34 --dir=/data/sys	#nfs安装
lang en_US.UTF-8	 #字符集
keyboard us	
network --device=eth0 --bootproto=dhcp --onboot=on --hostname lie-pc
#network --device=eth1 --bootproto=dhcp --onboot=on
#网络配置

#root password
rootpw 123456	 #root用户密码，也可以使用下面加密方式
#rootpw --iscrypted $1$dQVhf2.F$wUhtrf7gHDN8Q6EtlrWSF.


authconfig --enableshadow --enablemd5	 #系统认证信息
selinux --disabled	 #关闭selinux
firewall --disabled #关闭iptables
timezone --utc Asia/Shanghai #时区
bootloader --location=mbr --driveorder=sda
firstboot --disabled #禁止安装后的Agent设置
logging --level=info #日志级别
zerombr	 #清除mbr引导信息

##### 系统分区设置 #####
#clearpart --linux
clearpart --all
part /boot --fstype ext3 --size=200 --asprimary
part swap --size=512 --asprimary
part / --fstype ext3 --size=1 --grow --asprimary
reboot	 #安装后重启

##### 安装包的选择 #####
%packages
@base
@core
@development-libs
@development-tools
@editors
@system-tools
@x-software-development


##### 安装后的操作 #####

%post
#!/bin/bash
mkdir -p /application/tools
mkdir -p /server/scripts
```

#5. OS安装过程

在服务器上执行上面一键安装脚本(auto_install_centos.sh)之后， 新建一个VM虚拟机,直接启动！
![alt txt](/images/2013/pxe-dhcp-nfs-kickstart/02.png)

![alt txt](/images/2013/pxe-dhcp-nfs-kickstart/03.png)
