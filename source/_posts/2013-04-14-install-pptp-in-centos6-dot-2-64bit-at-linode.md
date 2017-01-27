---
layout: post
title: "centos6.2 64bit上安装pptp与客户端设置"
date: 2013-04-14 10:53
comments: true
categories: vpn
---

主要内容

```
#1.安装软件包
#2.vi /etc/pptpd.conf 文件
#3.vi /etc/ppp/options.pptpd
#4.设置使用 pptpd 的用户名和密码
#5.修改内核设置/etc/sysctl.conf
#6.添加 iptables 转发规则
#7.启动pptpd 服务
#8.一键安装shell脚本
#9.pptp客户端连接
#10.客户端vpn翻墙设置
```
<!-- more -->

pptp安装可以参考这篇文章

	在 Linode VPS 下搭建 pptp 服务器		
	http://www.chenjunlu.com/2012/04/how-to-setup-a-pptp-vpn-server-under-linode-vps/


#1.安装软件包

```
yum install -y ppp iptables
#wget http://poptop.sourceforge.net/yum/stable/packages/pptpd-1.3.4-2.el6.x86_64.rpm
wget http://poptop.sourceforge.net/yum/beta/rhel6/x86_64/pptpd-1.4.0-1.el6.x86_64.rpm

rpm -ivh pptpd-1.4.0-1.el6.x86_64.rpm
```


#2. vi /etc/pptpd.conf 文件

	#localip 192.168.0.1
	#remoteip 192.168.0.234-238,192.168.0.245

修改成

	localip 192.168.0.1
	remoteip 192.168.0.234-238,192.168.0.245

注意：此处的  remoteip 指定的 IP 范围是用来给远程连接使用的。


#3. vi /etc/ppp/options.pptpd

	#ms-dns 10.0.0.1
	#ms-dns 10.0.0.2
	
改成

	ms-dns 8.8.8.8
	ms-dns 8.8.4.4

#4.设置使用 pptpd 的用户名和密码
	vi /etc/ppp/chap-secrets
打开后只有两行，而且一个账号都没有

	# Secrets for authentication using CHAP
	# client        server  secret                  IP addresses

根据您的需要添加账号，每行一个。
按照：“用户名 pptpd 密码 ip地址”的格式输入，每一项之间用空格分开，例如：
	
	vpnuser pptpd password *	
	
#5.修改内核设置/etc/sysctl.conf

编辑 /etc/sysctl.conf 文件：

	sed -i 's/net.ipv4.ip_forward = 0/net.ipv4.ip_forward = 1/' /etc/sysctl.conf
	sed -i 's/net.ipv4.tcp_syncookies = 1/#net.ipv4.tcp_syncookies = 1/' /etc/sysctl.conf
	sysctl -p	
	
#6.添加 iptables 转发规则
```
iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE
/etc/init.d/iptables save
/etc/init.d/iptables restart
```

启动iptables 时，可能会遇到如下报错：
/etc/init.d/iptables restart
Setting chains to policy ACCEPT: security raw nat mangle fi[FAILED]

解决：
vi /etc/init.d/iptables
加入145到151行的内容,然后保存退出并重起
/etc/init.d/iptables restart

```
    security)
    $IPTABLES -t filter -P INPUT $policy \
        && $IPTABLES -t filter -P OUTPUT $policy \
        && $IPTABLES -t filter -P FORWARD $policy \
        || let ret+=1

    ;;
```

```
   142	    for i in $tables; do
   143		echo -n "$i "
   144		case "$i" in
   145	  security)
   146	    $IPTABLES -t filter -P INPUT $policy \
   147	        && $IPTABLES -t filter -P OUTPUT $policy \
   148	        && $IPTABLES -t filter -P FORWARD $policy \
   149	        || let ret+=1
   150	
   151	    ;;
   152		    raw)
   153			$IPTABLES -t raw -P PREROUTING $policy \
   154			    && $IPTABLES -t raw -P OUTPUT $policy \
   155			    || let ret+=1
   156			;;
```


#7.启动pptpd 服务
重启 pptpd 服务

	/etc/init.d/pptpd restart

配置 pptpd 随系统启动

	chkconfig pptpd on

确认1723端口开启

```
# netstat -lnt
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address               Foreign Address             State
tcp        0      0 0.0.0.0:1723                0.0.0.0:*                   LISTEN
...
```

至此 pptp 服务端安装全部结束


#8.一键安装shell脚本

```bash

#!/bin/bash
# install pptp vpn
######################
# Date : 2014-08-10
# Author: xionglie.qu
######################

echo "# 1. 安装基础包 "
yum install -y ppp iptables
wget http://poptop.sourceforge.net/yum/beta/rhel6/x86_64/pptpd-1.4.0-1.el6.x86_64.rpm
rpm -ivh pptpd-1.4.0-1.el6.x86_64.rpm

echo "# 2. vi /etc/pptpd.conf "
/bin/cp /etc/pptpd.conf /etc/pptpd.conf.`date +"%Y-%m-%d_%H-%M-%S"`
sed -i 's/#localip 192.168.0.1/localip 192.168.0.1/g' /etc/pptpd.conf
sed -i 's/#remoteip 192.168.0.234-238,192.168.0.245/remoteip 192.168.0.234-238,192.168.0.245/g' /etc/pptpd.conf

echo "# 3. vi /etc/ppp/options.pptpd "
/bin/cp /etc/ppp/options.pptpd /etc/ppp/options.pptpd.`date +"%Y-%m-%d_%H-%M-%S"`
sed -i 's/#ms-dns 10.0.0.1/ms-dns 8.8.8.8/g' /etc/ppp/options.pptpd
sed -i 's/#ms-dns 10.0.0.2/ms-dns 8.8.4.4/g' /etc/ppp/options.pptpd

echo "# 4.设置使用 pptpd 的用户名和密码 "
/bin/cp /etc/ppp/chap-secrets /etc/ppp/chap-secrets.`date +"%Y-%m-%d_%H-%M-%S"`
echo "###  /etc/ppp/chap-secrets "
echo "#vpnuser pptpd password *">> /etc/ppp/chap-secrets
echo "user001 pptpd pwd001 *    " >> /etc/ppp/chap-secrets

echo "# 5.修改内核设置/etc/sysctl.conf "
sed -i 's/net.ipv4.ip_forward = 0/net.ipv4.ip_forward = 1/' /etc/sysctl.conf
sed -i 's/net.ipv4.tcp_syncookies = 1/#net.ipv4.tcp_syncookies = 1/' /etc/sysctl.conf
sysctl -p	


echo "# 6. 添加 iptables 转发规则"
iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE
/etc/init.d/iptables save
/etc/init.d/iptables restart

echo "# 7.启动pptpd 服务"
/etc/init.d/pptpd restart
chkconfig pptpd on
chkconfig --list | grep pptpd

echo "# end #"

``` 
#9.pptp客户端连接
##mac
设置非常简单，不懂的话google一下"Mac PPTP VPN设置教程"即可有答案。


##windows 
可以看这篇文章

	怎么创建 VPN 连接（ Windows XP，pptp ）		
	http://lihua.me/zh/pptp-vpn-windows-xp/
	
#10.客户端vpn翻墙设置
pptp连接上去，必须将pptp设为默认连接，这样所有数据都会从vpn出去。

##mac下设置：
![pptp](/images/2013/04/pptp-mac-01.png)

![pptp](/images/2013/04/pptp-mac-02.png)

##windows xp下设置：
![pptp](/images/2013/04/pptp-xp.jpg)

 
####实现在使用vpn访问国外资源的同时, 能用非vpn线路高速访问本国资源.

	https://code.google.com/p/chnroutes/
	使用说明 
	https://code.google.com/p/chnroutes/wiki/Usage

(完)


