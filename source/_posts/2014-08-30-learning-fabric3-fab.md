---
layout: post
title: "Fabric-03 fab命令"
date: 2014-08-30 08:52
comments: true
categories: fabric
---

Fabric最常用的命令行工具是fab。当Fabric安装的时候，我们都能在环境变量中找到它。

官方usage说明：[fab options and arguments](http://docs.fabfile.org/en/1.9/usage/fab.html)

主要内容

```
#1.基本使用方法
#2.命令行选项
#3.Per-task参数
##3.1 Per-task语法定义
##3.2 角色和主机 Roles and hosts
```
<!-- more -->

#1.基本使用方法

最常见的使用方法，使用时没有任何选项(options)

	$ fab task1 task2

上面命令会在fabfile定义的主机上执行task1，接着是task2。

另一种用法，估计就很少人会知道了，

	$ fab [options] -- [shell command]

-- 后面的[shell command]会使用Fabric的run命令来执行。如果我们在命令行上定义了主机，[shell command]相当于作为单行的匿名task来执行。

	$ fab -H host1,host2,host3 -- uname -a

与 执行下面文件

```
from fabric.api import run

def anonymous():
    run("uname -a")
```

的命令

	$ fab -H host1,host2,host3 anonymous

是一样的效果。	

测试：

```bash
[root@fabserver fabric]# cat fabfile.py
from fabric.api import run

def anonymous():
    run("uname -a")

[root@fabserver fabric]# fab -H host1,host2,host3 -- uname -a
[host1] Executing task '<remainder>'
[host1] run: uname -a
[host1] out: Linux host1 2.6.32-431.el6.x86_64 #1 SMP Fri Nov 22 03:15:09 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux
[host1] out:

[host2] Executing task '<remainder>'
[host2] run: uname -a
[host2] out: Linux host2 2.6.32-431.el6.x86_64 #1 SMP Fri Nov 22 03:15:09 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux
[host2] out:

[host3] Executing task '<remainder>'
[host3] run: uname -a
[host3] out: Linux host3 2.6.32-431.el6.x86_64 #1 SMP Fri Nov 22 03:15:09 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux
[host3] out:


Done.
Disconnecting from host2... done.
Disconnecting from host3... done.
Disconnecting from host1... done.

[root@fabserver fabric]# fab -H host1,host2,host3 anonymous
[host1] Executing task 'anonymous'
[host1] run: uname -a
[host1] out: Linux host1 2.6.32-431.el6.x86_64 #1 SMP Fri Nov 22 03:15:09 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux
[host1] out:

[host2] Executing task 'anonymous'
[host2] run: uname -a
[host2] out: Linux host2 2.6.32-431.el6.x86_64 #1 SMP Fri Nov 22 03:15:09 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux
[host2] out:

[host3] Executing task 'anonymous'
[host3] run: uname -a
[host3] out: Linux host3 2.6.32-431.el6.x86_64 #1 SMP Fri Nov 22 03:15:09 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux
[host3] out:


Done.
Disconnecting from host2... done.
Disconnecting from host3... done.
Disconnecting from host1... done.
```

#2.命令行选项

fab命令的选项(options)基本上都可以对应上fabfile中的env变量。

如-H HOSTS, --hosts=HOSTS 有  env.hosts 

```bash
# fab --help
Usage: fab [options] <command>[:arg1,arg2=val2,host=foo,hosts='h1;h2',...] ...

Options:
  -h, --help            show this help message and exit
  -d NAME, --display=NAME 			#显示命令[NAME]的细节信息
                        print detailed info about command NAME
  -F FORMAT, --list-format=FORMAT 	#格式
                        formats --list, choices: short, normal, nested
  -I, --initial-password-prompt
                        Force password prompt up-front
  -l, --list            #列出可以执行的命令
  						print list of possible commands and exit 
  --set=KEY=VALUE,...   #设置env的key=value对
  						comma separated KEY=VALUE pairs to set Fab env vars 
  --shortlist           alias for -F short --list
  -V, --version         show program's version number and exit
  -a, --no_agent        don't use the running SSH agent
  -A, --forward-agent   forward local agent to remote end
  --abort-on-prompts    abort instead of prompting (for password, host, etc)
  -c PATH, --config=PATH
                        specify location of config file to use
  --colorize-errors     Color error output
  -D, --disable-known-hosts
                        do not load user known_hosts file
  -e, --eagerly-disconnect
                        disconnect from hosts as soon as possible
  -f PATH, --fabfile=PATH #fabric python模块文件路径
                        python module file to import, e.g. '../other.py'
  -g HOST, --gateway=HOST #网关
                        gateway host to connect through
  --hide=LEVELS         comma-separated list of output levels to hide
  -H HOSTS, --hosts=HOSTS #主机定义，多个以逗号分开
                        comma-separated list of hosts to operate on
  -i PATH               path to SSH private key file. May be repeated.
  -k, --no-keys         don't load private key files from ~/.ssh/
  --keepalive=N         enables a keepalive every N seconds
  --linewise            print line-by-line instead of byte-by-byte
  -n M, --connection-attempts=M 	#连接尝试次数
                        make M attempts to connect before giving up
  --no-pty              do not use pseudo-terminal in run/sudo
  -p PASSWORD, --password=PASSWORD  #授权或sudo时使用的密码
                        password for use with authentication and/or sudo
  -P, --parallel        #并行执行
  						default to parallel execution method 
  --port=PORT           SSH connection port #ssh端口
  -r, --reject-unknown-hosts #主机不存在于SSH known_hosts文件中，就会中止连接
                        reject unknown hosts
  --system-known-hosts=SYSTEM_KNOWN_HOSTS
                        load system known_hosts file before reading user
                        known_hosts
  -R ROLES, --roles=ROLES #角色
                        comma-separated list of roles to operate on
  -s SHELL, --shell=SHELL #远程执行命令的shell
                        specify a new shell, defaults to '/bin/bash -l -c'
  --show=LEVELS         comma-separated list of output levels to show
  --skip-bad-hosts      skip over hosts that can't be reached
  --ssh-config-path=PATH
                        Path to SSH config file
  -t N, --timeout=N     			#ssh连接超时时间
  						set connection timeout to N seconds 

  -T N, --command-timeout=N 		#ssh远程命令执行超时时间
                        set remote command timeout to N seconds
  -u USER, --user=USER  #远程主机的用户名
  						username to use when connecting to remote hosts 
  -w, --warn-only       warn, instead of abort, when commands fail
  -x HOSTS, --exclude-hosts=HOSTS 	#哪些主机不需要执行命令
                        comma-separated list of hosts to exclude
  -z INT, --pool-size=INT 			#并行执行时，并发线程数
                        number of concurrent processes to use in parallel mode
```

测试：

#####选项 -d 

```bash
[root@fabserver fabric]# cat fabfile.py
def hello(name="world"):
    print("Hello %s!" % name)
[root@fabserver fabric]# fab -d hello
Displaying detailed information for task 'hello':

    No docstring provided
    Arguments: name='world'
```



#3.Per-task参数

##3.1 Per-task语法定义

上面"2.命令行选项"提到的命令行选项是全局有效。如果不是变量又在其它地方覆盖命令行选项值的话，这些定义对所有task都是有效的。

我们可以针对每个task，定义不同的参数，这就是“per-task arguments”。

“per-task arguments”语法定义：

  (1) 使用:分隔任务(task)和与参数(arguments)

  (2) 使用,分隔不同的参数(arguments),参数需要反斜线(\)进行转义,如\,

  (3) 使用=进行keyword arguments的赋值，或根据位置参数进行定义。同样需要使用反斜线进行转义。

例子：

```bash
[root@fabserver fabric]# cat fabfile.py
def new_user(username, admin='no', comment="No comment provided"):
    print("New User (%s): %s" % (username, comment))
    pass
[root@fabserver fabric]# fab -l
Available commands:

    new_user
[root@fabserver fabric]#

#仅定义username
[root@fabserver fabric]# fab new_user:myusername
New User (myusername): No comment provided

Done.
[root@fabserver fabric]# fab new_user:username=myusername
New User (myusername): No comment provided

Done.

#使用位置参数
[root@fabserver fabric]# fab new_user:myusername,yes
New User (myusername): No comment provided

Done.

#混合使用
[root@fabserver fabric]# fab new_user:myusername,admin=yes
New User (myusername): No comment provided

Done.
#使用转义
[root@fabserver fabric]# fab new_user:myusername,admin=no,comment='Gary\, new developer (starts Monday)'
New User (myusername): Gary, new developer (starts Monday)

Done.
```
##3.2 角色和主机 Roles and hosts

常用的参数：host, hosts, role and roles

注意：hosts的主机是以;隔开。

```bash
[root@fabserver fabric]# fab new_user:myusername,hosts="host1;host2"
[host1] Executing task 'new_user'
New User (myusername): No comment provided
[host2] Executing task 'new_user'
New User (myusername): No comment provided

Done.
```
