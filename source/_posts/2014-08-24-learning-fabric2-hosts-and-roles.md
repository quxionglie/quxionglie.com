---
layout: post
title: "Fabric-02 主机(Hosts)和角色(Roles)"
date: 2014-08-24 14:27
comments: true
categories: fabric
---

主要内容

```
#1.定义主机列表
##1.1 主机
##1.2 角色
#2.如何构建主机列表(host lists) ?
##2.1 四种主机定义方式
###(1)Globally, via env
###(2)Globally, via the command line
###(3)Per-task, via the command line
###(4)Per-task, via decorators
##2.2 主机定义的优先顺序
##2.3 测试
#3. 其它
##3.1 合并主机列表
##3.2 重复的主机
##3.3 不包含指定的主机
##3.4 使用execute智能执行task
```
<!-- more -->

#1.定义主机列表
##1.1 主机
主机的定义格式为: username@hostname:port

username和port是可选的(关联@和:),默认是本地用户，端口是22.

ip6格式同样是支持的。例如::1, [::1]:1222, user@2001:db8::1 or user@[2001:db8::1]:1222

##1.2 角色

有时候我们希望给主机分组，这样我们就能方便的控制它们。

```python
from fabric.api import env

env.roledefs['webservers'] = ['www1', 'www2', 'www3']
```


```python
from fabric.api import env

env.roledefs = {
    'web': ['www1', 'www2', 'www3'],
    'dns': ['ns1', 'ns2']
}
```

角色定义并是可必须的，它只是能让我们方便的管理同样功能的服务器组。



#2.如何构建主机列表(host lists) ?

##2.1 四种主机定义方式

###(1)Globally, via env
最常见的方式是通过设置 env: hosts and roles 。

hosts和roles的值会在运行时进行检测，为每个task构造主机列表。

####例子1： fabfile.py

```python
from fabric.api import env, run

env.hosts = ['host1', 'host2']

def mytask():
    run('ls /var/www')
```

执行结果: 

```
# fab mytask
[host1] Executing task 'mytask'
[host1] run: ls /var/www
[host1] out: cgi-bin  error	html  icons
[host1] out:

[host2] Executing task 'mytask'
[host2] run: ls /var/www
[host2] out: cgi-bin  error	html  icons
[host2] out:


Done.
Disconnecting from host2... done.
Disconnecting from host1... done.
```
####例子2： 动态设置env.hosts
我们可以在task中设置env.hosts, 它会影响接下来执行的tasks

```
from fabric.api import env, run

def set_hosts():
    env.hosts = ['host1', 'host2']

def mytask():
    run('ls /var/www')
```

执行命令: 
	
	fab set_hosts mytask

跟例子1是一样的效果。

###(2)Globally, via the command line
你可在在模块中通过 env.hosts, env.roles, env.exclude_hosts 来定义主机，也可以通过命令行参数 [hosts/-H](http://docs.fabfile.org/en/1.9/usage/fab.html#cmdoption-H) 和 [--roles/-R](http://docs.fabfile.org/en/1.9/usage/fab.html#cmdoption-R) 来定义主机。

例如: 

	$ fab -H host1,host2 mytask


非常重要的一点是：
>命令行参数会在fabfile文件载入方前被解释，所以fabfile文件中定义的env.hosts or env.roles会覆盖命令行参数。
>如果你想合并它们,让在fabfile中使用 env.hosts.extend() 代替。

```python
from fabric.api import env, run

env.hosts.extend(['host3', 'host4'])

def mytask():
    run('ls /var/www')
```

执行： fab -H host1,host2 mytask, 主机会包括： ['host1', 'host2', 'host3', 'host4']

###(3)Per-task, via the command line
对每个task, 使用命令行定义hosts。

```
from fabric.api import run

def mytask():
    run('ls /var/www')
```

为mytask定义主机

	$ fab mytask:hosts="host1;host2"

这个会覆盖所有的主机列表，mytask只会在host1;host2中执行。

```bash
$ cat fabfile.py
from fabric.api import env, run

def mytask():
    run('ls /var/www')

$ fab mytask:hosts="host1;host2"
[host1] Executing task 'mytask'
[host1] run: ls /var/www
[host1] out: cgi-bin  error	html  icons
[host1] out:

[host2] Executing task 'mytask'
[host2] run: ls /var/www
[host2] out: cgi-bin  error	html  icons
[host2] out:


Done.
Disconnecting from host2... done.
Disconnecting from host1... done.
```


###(4)Per-task, via decorators

可以针对每一个task, 可以使用 hosts 或 roles decorators。

```python
from fabric.api import hosts, run

@hosts('host1', 'host2')
def mytask():
    run('ls /var/www')

```

或者这样定义

```
my_hosts = ('host1', 'host2')
@hosts(my_hosts)
def mytask():
    run('ls /var/www')
```

##2.2 主机定义的优先顺序

官方说明： [Order of precedence](http://docs.fabfile.org/en/1.9/usage/execution.html#order-of-precedence)

 1. Per-task, command-line host lists (fab mytask:host=host1) override absolutely everything else.
 2. Per-task, decorator-specified host lists (@hosts('host1')) override the env variables.
 3. Globally specified host lists set in the fabfile (env.hosts = ['host1']) can override such lists set on the command-line, but only if you’re not careful (or want them to.)
 4. Globally specified host lists set on the command-line (--hosts=host1) will initialize the env variables, but that’s it.

说明：

 1. Per-task, command-line host lists (fab mytask:host=host1) 会绝对覆盖所有的hosts定义。
 2. Per-task, decorator-specified host lists (@hosts('host1')) 会覆盖 env 变量(env variables)。
 3. Globally specified host lists set in the fabfile (env.hosts = ['host1']) 会覆盖command-lin定义的主机列表。
 4. Globally specified host lists set on the command-line (--hosts=host1) 会初始化env 变量(env variables), 但仅仅如此。

##2.3 测试
###2.3.1 测试1

```python
from fabric.api import env, hosts, run

env.hosts = ['host1']

def mytask():
    run('ls /var/www')
```

执行命令：

```
# fabfile定义的env.hosts会覆盖命令行的定义(-H)
[root@fabserver fabric]# fab -H host2 mytask
[host1] Executing task 'mytask'
[host1] run: ls /var/www
[host1] out: cgi-bin  error	html  icons
[host1] out:


Done.
Disconnecting from host1... done.

# 命令行task里面的主机定义会覆盖掉"fabfile定义的env.hosts"及"命令行里的定义(-H)"
[root@fabserver fabric]# fab -H host2 mytask:hosts=host3
[host3] Executing task 'mytask'
[host3] run: ls /var/www
[host3] out: cgi-bin  error	html  icons
[host3] out:


Done.
Disconnecting from host3... done.
```


###2.3.2 测试2

```
from fabric.api import env, hosts, run

env.hosts = ['host1']

@hosts('host2')
def mytask():
    run('ls /var/www')
```

执行命令：

```bash
# fabfile里面的task hosts定义会覆盖env.hosts定义
[root@fabserver fabric]# fab mytask
[host2] Executing task 'mytask'
[host2] run: ls /var/www
[host2] out: cgi-bin  error	html  icons
[host2] out:


Done.
Disconnecting from host2... done.
[root@fabserver fabric]# fab -H 'host3' mytask
[host2] Executing task 'mytask'
[host2] run: ls /var/www
[host2] out: cgi-bin  error	html  icons
[host2] out:


Done.
Disconnecting from host2... done.
[root@fabserver fabric]# fab -H host3 mytask
[host2] Executing task 'mytask'
[host2] run: ls /var/www
[host2] out: cgi-bin  error	html  icons
[host2] out:


Done.
Disconnecting from host2... done.
 
# 命令行内的task hosts定义会覆盖所有定义
[root@fabserver fabric]# fab -H host3 mytask:hosts="host1"
[host1] Executing task 'mytask'
[host1] run: ls /var/www
[host1] out: cgi-bin  error	html  icons
[host1] out:


Done.
Disconnecting from host1... done.
```

#3. 其它

##3.1 合并主机列表
```python
from fabric.api import env, hosts, roles, run

env.roledefs = {'role1': ['host2', 'host3']}

@hosts('host1', 'host2')
@roles('role1')
def mytask():
    run('ls /var/www')

```

执行mytask时，[host1，host2，host3]都会执行。

```bash
[root@fabserver fabric]# fab mytask
[host1] Executing task 'mytask'
[host1] run: ls /var/www
[host1] out: cgi-bin  error	html  icons
[host1] out:

[host2] Executing task 'mytask'
[host2] run: ls /var/www
[host2] out: cgi-bin  error	html  icons
[host2] out:

[host3] Executing task 'mytask'
[host3] run: ls /var/www
[host3] out: cgi-bin  error	html  icons
[host3] out:


Done.
Disconnecting from host2... done.
Disconnecting from host3... done.
Disconnecting from host1... done.
```

##3.2 重复的主机
fabric会删除重复的host, 所以对于每个task,每个host只会执行一次。
如果不需要删除重复的主机，请设置 env.dedupe_hosts 为 False.

例如，下面的host2会执行两次

```bash
[root@fabserver fabric]# cat fabfile.py
from fabric.api import env, hosts, roles, run

env.roledefs = {'role1': ['host2', 'host3']}
env.dedupe_hosts = False

@hosts('host1', 'host2')
@roles('role1')
def mytask():
    run('ls /var/www')

[root@fabserver fabric]# fab mytask
[host1] Executing task 'mytask'
[host1] run: ls /var/www
[host1] out: cgi-bin  error	html  icons
[host1] out:

[host2] Executing task 'mytask'
[host2] run: ls /var/www
[host2] out: cgi-bin  error	html  icons
[host2] out:

[host2] Executing task 'mytask'
[host2] run: ls /var/www
[host2] out: cgi-bin  error	html  icons
[host2] out:

[host3] Executing task 'mytask'
[host3] run: ls /var/www
[host3] out: cgi-bin  error	html  icons
[host3] out:


Done.
Disconnecting from host2... done.
Disconnecting from host3... done.
Disconnecting from host1... done.
```

##3.3 不包含指定的主机
在命令行中使用 --exclude-hosts/-x 定义

```bash
[root@fabserver fabric]# cat fabfile.py
from fabric.api import env, hosts, roles, run

env.roledefs = {'role1': ['host2', 'host3']}

@hosts('host1', 'host2')
@roles('role1')
def mytask():
    run('ls /var/www')

#注意，这里声明排除host1是不起作用的
[root@fabserver fabric]# fab mytask -x host1
[host1] Executing task 'mytask'
[host1] run: ls /var/www
[host1] out: cgi-bin  error	html  icons
[host1] out:

[host2] Executing task 'mytask'
[host2] run: ls /var/www
[host2] out: cgi-bin  error	html  icons
[host2] out:

[host3] Executing task 'mytask'
[host3] run: ls /var/www
[host3] out: cgi-bin  error	html  icons
[host3] out:


Done.
Disconnecting from host2... done.
Disconnecting from host3... done.
Disconnecting from host1... done.

#成功排除host1
[root@fabserver fabric]# fab mytask:exclude_hosts="host1"
[host2] Executing task 'mytask'
[host2] run: ls /var/www
[host2] out: cgi-bin  error	html  icons
[host2] out:

[host3] Executing task 'mytask'
[host3] run: ls /var/www
[host3] out: cgi-bin  error	html  icons
[host3] out:


Done.
Disconnecting from host2... done.
Disconnecting from host3... done.
```

注意：
Host exclusion lists 跟 host lists 一样，是不能在不同的级别(level)合并在一起的。

例如：全局的 -x 选项不会影响到per-task host list 设置的。同样的per-task的exclude_hosts会影响全局的-H 定义的主机列表。

##3.4 使用execute智能执行task
###3.4.1 智能执行task

```
from fabric.api import env, run, roles, execute

env.roledefs = {
    'db': ['host1', 'host2'],
    'web': ['host3'],
}

@roles('db')
def migrate():
    # Database stuff here.
    pass

@roles('web')
def update():
    # Code updates here.
    pass

def deploy():
    execute(migrate)
    execute(update)

```

执行 $ fab migrate update 和 $ fab deploy 是一样的效果。

```bash
[root@fabserver fabric]# fab migrate update
[host1] Executing task 'migrate'
[host2] Executing task 'migrate'
[host3] Executing task 'update'

Done.
[root@fabserver fabric]# fab deploy
[host1] Executing task 'migrate'
[host2] Executing task 'migrate'
[host3] Executing task 'update'

Done.
```

###3.4.2 动态设置主机列表

```python
from fabric.api import run, execute, task

# For example, code talking to an HTTP API, or a database, or ...
from mylib import external_datastore

# This is the actual algorithm involved. It does not care about host
# lists at all.
def do_work():
    run("something interesting on a host")

# This is the user-facing task invoked on the command line.
@task
def deploy(lookup_param):
    # This is the magic you don't get with @hosts or @roles.
    # Even lazy-loading roles require you to declare available roles
    # beforehand. Here, the sky is the limit.
    host_list = external_datastore.query(lookup_param)
    # Put this dynamically generated host list together with the work to be
    # done.
    execute(do_work, hosts=host_list)
```

发布到app服务主机

	$ fab deploy:app

发布到db服务主机

	$ fab deploy:db

