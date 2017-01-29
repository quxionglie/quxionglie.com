---
title: phabricator安装与使用
tags: 
---

# 环境

OS

```
[root@n2 ~]# cat /etc/redhat-release 
CentOS release 6.8 (Final)
[root@n2 ~]# uname -a
Linux n2 2.6.32-642.el6.x86_64 #1 SMP Tue May 10 17:27:01 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux
```


# phabricator安装
## 基础
```
yum -y install git 
```

## nginx

vi /etc/yum.repos.d/nginx.repo

```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/6/$basearch/
gpgcheck=0
enabled=1
```

yum -y install nginx

vi /etc/nginx/conf.d/phabricator.conf 


```conf
server {
  listen       8088;
  server_name  localhost;
  #server_name phabricator.example.com;

  root        /home/work/phabricator/webroot;

  location / {
    index index.php;
    rewrite ^/(.*)$ /index.php?__path__=/$1 last;
  }

  location /index.php {
    fastcgi_pass   localhost:9000;
    fastcgi_index   index.php;

    #required if PHP was built with --enable-force-cgi-redirect
    fastcgi_param  REDIRECT_STATUS    200;

    #variables to make the $_SERVER populate in PHP
    fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
    fastcgi_param  QUERY_STRING       $query_string;
    fastcgi_param  REQUEST_METHOD     $request_method;
    fastcgi_param  CONTENT_TYPE       $content_type;
    fastcgi_param  CONTENT_LENGTH     $content_length;

    fastcgi_param  SCRIPT_NAME        $fastcgi_script_name;

    fastcgi_param  GATEWAY_INTERFACE  CGI/1.1;
    fastcgi_param  SERVER_SOFTWARE    nginx/$nginx_version;

    fastcgi_param  REMOTE_ADDR        $remote_addr;
  }
}
```


## mysql5.6

```
wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
rpm -ivh mysql-community-release-el6-5.noarch.rpm
yum install mysql-community-server
```

grant all privileges on *.* to work@'%' identified by 'work';


## php

```
yum -y install git php php-cli php-mysql php-process php-devel php-gd php-pecl-apc php-pecl-json php-mbstring 
```

启动PHP-CGI，使用如下命令：

```
/bin/cp /etc/rc.local /etc/rc.local.`date +"%Y-%m-%d_%H-%M-%S"`

if [ `grep php-cgi /etc/rc.local|wc -l` -lt 1 ];then
cat >>/etc/rc.local <<EOF
# php
/usr/bin/php-cgi -b 127.0.0.1:9000
EOF
fi
```

## phabricator


```
useradd work
su - work

if [[ ! -e libphutil ]]
then
  git clone https://github.com/phacility/libphutil.git
else
  (cd libphutil && git pull --rebase)
fi

if [[ ! -e arcanist ]]
then
  git clone https://github.com/phacility/arcanist.git
else
  (cd arcanist && git pull --rebase)
fi

if [[ ! -e phabricator ]]
then
  git clone https://github.com/phacility/phabricator.git
else
  (cd phabricator && git pull --rebase)
fi
```
