---
layout: post
title: "Fabric-01 安装与使用"
date: 2014-08-11 14:56
comments: true
categories: fabric
---

#1.Fabric是什么？

Fabric is a Python (2.5-2.7) library and command-line tool for streamlining the use of SSH for application deployment or systems administration tasks.

Fabric是一个Python(2.5-2.7)库，可以通过SSH在多个主机上批量执行任务。Fabric非常适合于应用的(自动化)部署或者执行系统管理任务。

<!-- more -->

#2.Fabric安装

Fabric强烈推荐使用pip安装:
	
	$ pip install fabric
	
easy_install 虽然比较老了，但同样也可使用。

	$ easy_install fabric


安装过程中，可能会用到的命令

```bash
#安装好编译环境
yum -y install gcc gcc-c++ automake autoconf libtool make 

#会安装好easy_install
yum -y install python-setuptools  
yum -y install python-devel
easy_install pip
#pip install paramiko
pip install paramiko==1.10
pip install pycrypto
pip install fabric
```

###2.1. 可能会遇到的错误:

错误(1)：

```
Running pycrypto-2.6.1/setup.py -q bdist_egg --dist-dir /tmp/easy_install-xBCzLD/pycrypto-2.6.1/egg-dist-tmp-6Y0X6m
warning: GMP or MPIR library not found; Not building Crypto.PublicKey._fastmath.
src/MD2.c:31:20: error: Python.h: No such file or directory
src/MD2.c:131: error: expected ‘=’, ‘,’, ‘;’, ‘asm’ or ‘__attribute__’ before ‘*’ token
```
解决：
	yum -y install python-devel

---

错误(2)：

```
[root@fabric2 ~]# fab
Traceback (most recent call last):
  File "/usr/bin/fab", line 5, in <module>
    from pkg_resources import load_entry_point
  File "/usr/lib/python2.6/site-packages/pkg_resources.py", line 2655, in <module>
    working_set.require(__requires__)
  File "/usr/lib/python2.6/site-packages/pkg_resources.py", line 648, in require
    needed = self.resolve(parse_requirements(requirements))
  File "/usr/lib/python2.6/site-packages/pkg_resources.py", line 546, in resolve
    raise DistributionNotFound(req)
pkg_resources.DistributionNotFound: paramiko>=1.10
```

解决：paramiko版本过高，paramiko>=1.10

```
pip uninstall fabric paramiko
pip install paramiko==1.10
pip install fabric
```

#3.Fabric简单使用

测试的虚拟机环境：

```
[root@fabric1 ~]# cat /etc/redhat-release
CentOS release 6.3 (Final)
[root@fabric1 ~]# uname -r
2.6.32-279.el6.x86_64

三台服务器
192.168.27.137 host1
192.168.27.138 host2
192.168.27.139 host3
```

###(1)Hello, fab

fabfile.py

```python
def hello():
    print("Hello world!")
```

执行命令

```
# fab hello
Hello world!

Done.
```

当前工作目录中默认的模块名称是：fabfile.py, hello函数可以使用fab工具执行。

####任务参数 Task arguments

调用模式： 

```
<task name>:<arg>,<kwarg>=<value>,....  
``` 

```
[root@fabric1 scritps]# cat fabfile.py
def hello(name="world"):
    print("Hello %s!" % name)

[root@fabric1 scritps]# fab hello
Hello world!

Done.
[root@fabric1 scritps]# fab hello:name=Jeff
Hello Jeff!

Done.
[root@fabric1 scritps]# fab hello:Jeff
Hello Jeff!

Done.
```

###(2)本地(local)命令与远程命令

uname.py

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
from fabric.api import local,run

def test():
    local("uname -r")
    run("uname -r")
```


执行：fab -f uname.py test


```
# fab -f uname.py -H root@192.168.27.138 test
[root@192.168.27.138] Executing task 'test'
[localhost] local: uname -r
2.6.32-279.el6.x86_64
[root@192.168.27.138] run: uname -r
[root@192.168.27.138] Login password for 'root': #这里要输入密码
[root@192.168.27.138] out: 2.6.32-279.el6.x86_64
[root@192.168.27.138] out:


Done.
Disconnecting from 192.168.27.138... done.
```

local执行本地命令，run执行远程命令。

-H 是定义主机地址。


###(3)更加实用的例子

test.py

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
from fabric.api import settings, run, cd, env

env.hosts = ['work@host2']
env.user = 'work'
env.password = '123456'

def deploy():
    code_dir = '/home/work/GoFileServer'
    with settings(warn_only=True):
        if run("test -d %s" % code_dir).failed:
            run("git clone https://github.com/xionglie/GoFileServer.git %s" % code_dir)
    with cd(code_dir):
        run("git pull")
```    

说明：

这里我将远程主机的用户名、密码写在python文件里, 生产环境是不推荐这么做的。   
下一篇文章，我会重点讲解hosts,user,password的相关问题。

deploy是将代码发布到远程主机上，如果远程项目目录还未存在，则先执行git clone操作。

最后到代码目录里执行 git pull , 更新代码到最新版本。 


执行结果：

```
[root@fabric1 scritps]# fab -f test.py deploy
[work@host2] Executing task 'deploy'
[work@host2] run: test -d /home/work/GoFileServer

Warning: run() received nonzero return code 1 while executing 'test -d /home/work/GoFileServer'!

[work@host2] run: git clone https://github.com/xionglie/GoFileServer.git /home/work/GoFileServer
[work@host2] out: Initialized empty Git repository in /home/work/GoFileServer/.git/
[work@host2] out: remote: Counting objects: 5, done.
[work@host2] out: remote: Total 5 (delta 0), reused 0 (delta 0)
[work@host2] out: Unpacking objects:  20% (1/5)
[work@host2] out: Unpacking objects:  40% (2/5)
[work@host2] out: Unpacking objects:  60% (3/5)
[work@host2] out: Unpacking objects:  80% (4/5)
[work@host2] out: Unpacking objects: 100% (5/5)
[work@host2] out: Unpacking objects: 100% (5/5), done.
[work@host2] out:

[work@host2] run: git pull
[work@host2] out: Already up-to-date.
[work@host2] out:


Done.
Disconnecting from host2... done.
```



