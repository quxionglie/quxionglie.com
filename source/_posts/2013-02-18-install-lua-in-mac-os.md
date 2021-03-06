---
layout: post
title: "在MacOS上安装lua"
date: 2013-02-18 14:32
comments: true
categories: lua
---
下午心血来潮，想在MacOS上安装lua，但使用brew install lua安装lua报错，于是使用源码安装。
 
<!-- more -->

lua官网：[http://www.lua.org/](http://www.lua.org/)

# 使用命令brew install lua安装


```bash
xionglie@lie-mac:~ $ brew install lua
==> Downloading http://www.lua.org/ftp/lua-5.1.4.tar.gz
Already downloaded: /Library/Caches/Homebrew/lua-5.1.4.tar.gz
==> Patching
patching file Makefile
patching file src/Makefile
######################################################################## 100.0%
patching file lcode.c
patching file ldblib.c
patching file liolib.c
patching file llex.c
patching file loadlib.c
patching file lstrlib.c
patching file lvm.c
Error: Permission denied - /usr/local/lib/lua
xionglie@lie-mac:~ $ sudo brew install lua
Password:
Error: Cowardly refusing to `sudo brew install'
You can use brew with sudo, but only if the brew executable is owned by root.
However, this is both not recommended and completely unsupported so do so at
your own risk.

xionglie@lie-mac:~ $ ls -ld /usr/local/lib
drwxr-xr-x  5 24561  wheel  170 Feb 18 17:27 /usr/local/lib
#用户是24561，难道用户已经删除？
#后面一查确实是权限问题，使用下面两条命令可以解决
#sudo chown -R xionglie /usr/local/include
#sudo chown -R xionglie /usr/local/lib/
xionglie@lie-mac:~ $ ls -ld /usr/local/lib/lua
ls: /usr/local/lib/lua: No such file or directory
```

# 使用源码安装命令

```bash
wget http://www.lua.org/ftp/lua-5.2.1.tar.gz
tar zxf lua-5.2.1.tar.gz 
cd lua-5.2.1/src
make macosx
sudo cp lua /usr/bin/lua
```

# 测试：

```bash
xionglie@lie-mac:~ $ lua -v
Lua 5.2.1  Copyright (C) 1994-2012 Lua.org, PUC-Rio

xionglie@lie-mac:/tmp $ cat test.lua 
-- just test
print("Hello World")

xionglie@lie-mac:/tmp $ lua test.lua 
Hello World

```

\-----------------------------------------------
### 参考：How Do I Install Lua on MacOS?  
来源：http://stackoverflow.com/questions/5496003/how-do-i-install-lua-on-macos 
  
Compiling from source code is not that painful.  
Lua 5.1.4 here: http://www.lua.org/ftp/lua-5.1.4.tar.gz Lua 5.2 alpha here: http://www.lua.org/work/lua-5.2.0-alpha.tar.gz

Take Lua 5.2 as example:

```bash
#Open your Terminal.app 
wget http://www.lua.org/work/lua-5.2.0-alpha.tar.gz    
tar xvzf lua-5.2.0-alpha.tar.gz     
cd lua-5.2.0-alpha/src    
make macosx(I believe you have Xcode installed)      
#After that, you can see 'lua' binary under current dir.    
sudo cp lua /usr/bin/lua     
```

Now you can enter lua to have a try. :)   

