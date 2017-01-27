---
layout: post
title: "linux系列4 - pxe+dhcp+nfs+kickstart无人值守批量安装Centos6.x x64"
date: 2014-08-09 16:42
comments: true
categories: linux
---


#1. 基本说明

相关资料请参考: 
[linux系列2 - pxe+dhcp+nfs+kickstart无人值守批量安装Centos5.8 x64](/blog/2013/01/24/pxe-plus-dhcp-plus-nfs-plus-kickstart-centos5-dot-8-x64/)

###centos5.x与centos6.x不一致的地方：
(1)安装NFS时的RPC程序，centos5.x中叫做portmap，centos6.x叫rpcbind。

(2)tftp默认目录，centos5.x时是 /tftpboot ，centos6.x在 /var/lib/tftpboot

(3)dhcp配置文件位置, centos5.x时是 /etc/dhcpd.conf ，centos6.x在 /etc/dhcp/dhcpd.conf

<!-- more -->

#2. 一键安装脚本

```bash
#!/bin/bash
# auto install kickstart server
# the os root's passwd is 123456
#
# Filename: auto_install_centos6.x.sh
# Author: quxl
# Date:   2014-08-07  
# Email:  xionglie.qu@gmail.com  
#------------Environment------------  
#   Linux 2.6.32-279.el6.x86_64
#   CentOS release 6.3 (Final)
#-----------------------------------  

#192.168.3.128
KICKSTART_SERVER_IP=`/sbin/ifconfig eth0 | grep 'inet addr' | awk '{print $2}' | awk -F: '{print $2}'`

#192.168.3
KICKSTART_IP_SUBNET=`echo ${KICKSTART_SERVER_IP}  | awk -F '.' '{print $1"."$2"."$3}'`
KICKSTART_SERVER_SUBNET=${KICKSTART_IP_SUBNET}.0
KICKSTART_SERVER_NETMASK=255.255.255.0
KICKSTART_SERVER_ROUTE=${KICKSTART_IP_SUBNET}.2
KICKSTART_SERVER_DHCP_IP_START=${KICKSTART_IP_SUBNET}.128
KICKSTART_SERVER_DHCP_IP_END=${KICKSTART_IP_SUBNET}.254

#portmap,发现CentOS 6上不叫portmap，而是改为rpcbind
yum -y install rpcbind nfs-utils 
yum -y install syslinux

echo "----- 1.配置nfs服务器 --------------"
#创建共享目录
mkdir -p /data/sys
mount /dev/cdrom /data/sys
echo "mount /dev/cdrom /data/sys" >>/etc/rc.local 
ls -l /data/sys

#安装nfs,分发共享目录
rpm -qa | grep nfs
cat > /etc/exports << EOF
/data/sys ${KICKSTART_SERVER_SUBNET}/24(ro,sync)
/data/kickstart ${KICKSTART_SERVER_SUBNET}/24(ro,sync)
EOF
cat /etc/exports

#启动nfs,设置开机启动
/etc/init.d/rpcbind restart
/etc/init.d/nfs restart
showmount -e 127.0.0.1

chkconfig rpcbind on
chkconfig nfs on
chkconfig --list | egrep "nfs|rpcbind"

echo "----- 2.配置TFTP服务器 --------------"
yum -y install tftp-server* -y
sed -i '/server_args.*=/ s#/var/lib/tftpboot#/tftpboot/#' /etc/xinetd.d/tftp
sed -i '/disable.*=/ s#yes#no#' /etc/xinetd.d/tftp
/etc/init.d/xinetd restart
chkconfig xinetd on

#与 centos5.x位置不同
mkdir -p /tftpboot
/bin/cp /usr/share/syslinux/pxelinux.0 /tftpboot/
/bin/cp /data/sys/images/pxeboot/vmlinuz /tftpboot/
/bin/cp /data/sys/images/pxeboot/initrd.img /tftpboot/
ls -l /tftpboot/
#initrd.img  pxelinux.0  pxelinux.cfg  vmlinuz

mkdir /tftpboot/pxelinux.cfg -p
#/bin/cp /data/sys/isolinux/isolinux.cfg /tftpboot/pxelinux.cfg/default

cat > /tftpboot/pxelinux.cfg/default << EOF
default linux
#prompt 1
timeout 600

display boot.msg

menu background splash.jpg
menu title Welcome to CentOS 6.3!
menu color border 0 #ffffffff #00000000
menu color sel 7 #ffffffff #ff000000
menu color title 0 #ffffffff #00000000
menu color tabmsg 0 #ffffffff #00000000
menu color unsel 0 #ffffffff #00000000
menu color hotsel 0 #ff000000 #ffffffff
menu color hotkey 7 #ffffffff #ff000000
menu color scrollbar 0 #ffffffff #00000000

label linux
  menu label Install system
  menu default
  kernel vmlinuz
  append ks=nfs:${KICKSTART_SERVER_IP}:/data/kickstart/ks.cfg  initrd=initrd.img

EOF

chmod a+r /tftpboot/pxelinux.cfg/default

echo "----- 3.配置DHCP --------------"
yum -y install dhcp*
#/bin/cp /usr/share/doc/dhcp-4.1.1/dhcpd.conf.sample /etc/dhcpd.conf

# centos5.x，配置文件都被配置在/etc/dhcpd.conf,
# centos6.x, 以后放在/etc/dhcp/dhcpd.conf
cat > /etc/dhcp/dhcpd.conf << EOF
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
#出错的话，cat /var/log/messages

chkconfig dhcpd on
chkconfig --list dhcpd

echo "----- 4.配置kickstart --------------"
mkdir -p /data/kickstart/
#/bin/cp /root/anaconda-ks.cfg /data/kickstart/ks.cfg
#chmod 644 /data/kickstart/ks.cfg

cat > /data/kickstart/ks.cfg << EOF 
#platform=x86, AMD64, or Intel EM64T
#version=DEVEL
# Firewall configuration
firewall --disabled
# Install OS instead of upgrade
install
# Use NFS installation media
nfs --server=${KICKSTART_SERVER_IP} --dir=/data/sys
# Root password abc123
rootpw --iscrypted \$1\$A05l5MQt\$VAf56PD915R8SwtFYJSeE/
# System authorization information
auth  --useshadow  --passalgo=sha512
# Use text mode install
text
# System keyboard
keyboard us
# System language
lang en_US
# SELinux configuration
selinux --disabled
# Do not configure the X Window System
skipx
# Installation logging level
logging --level=info
# Reboot after installation
reboot
# System timezone
timezone --isUtc Asia/Shanghai
# Network information
network  --bootproto=dhcp --device=eth0 --onboot=on
# System bootloader configuration
bootloader --location=mbr
# Clear the Master Boot Record
zerombr
# Partition clearing information
clearpart --all --initlabel
# Disk partitioning information
part /boot --asprimary --fstype="ext4" --size=200
part swap --fstype="swap" --size=512
part / --asprimary --fstype="ext4" --grow --size=1

%post
#!/bin/bash
echo "nameserver 208.67.222.222" >>/etc/resolv.conf
echo "nameserver 208.67.220.220" >>/etc/resolv.conf

mkdir -p /application/tools
mkdir -p /server/{scripts,backup}

wget -nv -O /server/scripts/update_hostname.sh http://${KICKSTART_SERVER_IP}/update_hostname.sh
wget -nv -O /server/scripts/update_ip.sh http://${KICKSTART_SERVER_IP}/update_ip.sh
wget -nv -O /server/scripts/update_network.sh http://${KICKSTART_SERVER_IP}/update_network.sh

%end

%packages
@base
@development

%end
EOF

#stop iptables
#vi /etc/selinux/config
/etc/init.d/iptables stop
chkconfig iptables off

#stop selinux
setenforce 0
sed -i 's#SELINUX=enforcing#SELINUX=disabled#g' /etc/selinux/config 
cat /etc/selinux/config 

echo "-----done-----"

```

#3. 相关文件说明

### 3.1服务器环境说明
操作系统：

```
[root@ks2 ~]# cat /etc/redhat-release
CentOS release 6.3 (Final)
[root@ks2 ~]# uname -mi
x86_64 x86_64
[root@ks2 ~]# uname -r
2.6.32-279.el6.x86_64
```

主机网络参数设置：

```
网卡eth0: 192.168.27.134
默认网关: 192.168.27.2
子网掩码: 255.255.255.0
```

### 3.2 /etc/dhcp/dhcpd.conf

```
[root@ks2 ~]# cat /etc/dhcp/dhcpd.conf
ddns-update-style none;
ignore client-updates;
allow booting;
allow bootp;
default-lease-time 21600;
max-lease-time 43200;
option routers 192.168.27.2;

subnet 192.168.27.0 netmask 255.255.255.0 {
range dynamic-bootp 192.168.27.128 192.168.27.254;
next-server 192.168.27.134;
filename "/data/kickstart/ks.cfg";
next-server 192.168.27.134;
filename "pxelinux.0";
}
```

#4. 可能遇到的错误
###错误1：
在server端已经配置好了，在clinet端用网络引导时能得到地址，但总是报错：

PXE-T00 Permission deny

PXE-E36 Error received from tftp server

解决方法：关掉SElinux 

```
1、快速关闭SElinux，使用如下命令就可以：
　　/usr/sbin/setenforce 0 立刻关闭 SELINUX
　　/usr/sbin/setenforce 1 立刻启用 SELINUX
2、加到系统默认启动里面
　　echo "/usr/sbin/setenforce 0" >> /etc/rc.local
3、可以编辑配置文件达到同样的目的
vi /etc/selinux/conf
set SELINUX=disabled
```