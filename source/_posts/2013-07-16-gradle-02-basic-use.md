---
layout: post
title: "使用gradle构建项目02-基础使用"
date: 2013-07-16 12:01
comments: true
categories: gradle
---
本文包括以下内容： 		
(1)lib导入/导出   
(2)代码导入eclipse或IDEA   
(3)代码执行(java程序与web项目)   
(4)根据项目环境(local,dev,product等)打包     

建议大家遇到问题，先查看一下官方的文档，内容已经非常详细了。   
http://www.gradle.org/documentation  

<!-- more -->

# lib依赖
## 添加maven库    
build.gradle   

```
repositories {   
    mavenCentral()  
}
``` 

添加依赖的jar包 
搜索jar包的写法，可以到这个网去查 http://mvnrepository.com/
 
build.gradle

```
dependencies {
    compile group: 'commons-collections', name: 'commons-collections', version: '3.2'
    testCompile group: 'junit', name: 'junit', version: '4.+'
}
```

我的写法如下：

```
dependencies {
	providedCompile 'javax.servlet:jsp-api:2.0'
	providedCompile 'javax.servlet:servlet-api:2.5'	//省略...
	compile "ch.qos.logback:logback-classic:0.9.9"
	compile "ch.qos.logback:logback-core:0.9.9"
    testCompile "junit:junit:4.8.2"
}
```

## 引入不在maven库的包
在工作中，有些jar包是自己打的，或在版本库中根本找不到。怎么办呢？  
我的简单解决方法是，让gradle自己到指定的目录去找jar包

```
repositories {
	mavenCentral()
	flatDir dirs: "lib"
}

dependencies {
   	compile fileTree(dir: 'lib', includes: ['*.jar'])
   	//省略...    
	compile "ch.qos.logback:logback-classic:0.9.9"
	compile "ch.qos.logback:logback-core:0.9.9"
    testCompile "junit:junit:4.8.2"
}
```



# 代码导入eclipse或IDEA
要想让工程导入到eclipse或IDEA中，需生成一些文件，或eclipse的.project等    
build.gradle

```
apply plugin: 'eclipse'
apply plugin: 'idea'
```
执行gradle eclipse 会生成eclipse的工程文档。   
执行gradle idea 会生成idea的工程文档。  
具体使用请查看

[http://www.gradle.org/docs/current/userguide/eclipse_plugin.html](http://www.gradle.org/docs/current/userguide/eclipse_plugin.html)

[http://www.gradle.org/docs/current/userguide/idea_plugin.html](http://www.gradle.org/docs/current/userguide/idea_plugin.html)


# 代码执行(java程序与web项目)
## java应用
build.gradle

```
task(runMsgServer, dependsOn: 'classes', type: JavaExec) {
	main = 'com.xionglie.tcserver.Main'
	classpath = sourceSets.main.runtimeClasspath
}
defaultTasks 'runMsgServer'
```

命令行下直接执行 gradle ，就会执行com.xionglie.tcserver.Main的main方法。

## web项目
可以引入jetty插件  

```
apply plugin: 'jetty'
```

直接执行：$ gradle jettyRun 

详细使用可参考jetty插件说明，  
http://www.gradle.org/docs/current/userguide/jetty_plugin.html


# 环境配置,根据项目环境(local,product等)打包

为了方便地将应用部署到开发、测试以及产品等不同环境上， 我们必须在不同的环境使用不同的配置文件。

![Alt text](/images/2013/07/gradle02.png)

```
// gradle jar -Denv=local
def env = System.getProperty("env")?:"local"

sourceSets {
     main {
         resources {
             exclude 'src/main/resources'
             srcDirs = ["src/main/resources/public","src/main/resources/$env"]
         }
    }
}
```

打jar包  
本地local环境   
$ gradle jar -Denv=local  

生产product环境  
$ gradle jar -Denv=product  

