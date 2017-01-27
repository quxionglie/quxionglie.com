---
layout: post
title: "使用gradle构建项目01-建立项目目录结构 "
date: 2013-02-17 22:01
comments: true
categories: gradle
---
gradle的目录的结构规范和maven是一样的，我们来看一下Maven标准目录结构  

<!-- more -->

Introduction to the Standard Directory Layout  
[http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html](http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html )

```
src/main/java		#Application/Library sources
src/main/resources	#Application/Library resources
src/main/filters	#Resource filter files
src/main/assembly	#Assembly descriptors
src/main/config		#Configuration files
src/main/scripts	#Application/Library scripts
src/main/webapp		#Web application sources
src/test/java		#Test sources
src/test/resources	#Test resources
src/test/filters	#Test resource filter files
src/site			#Site
LICENSE.txt			#Project's license
NOTICE.txt			#Notices and attributions required by libraries that the project depends on
README.txt			#Project's readme
```

## 方法1：使用shell脚本
```bash
	project_name=testProject  
	mkdir -p ${project_name}  
	cd ${project_name} 
	mkdir -p src/main/{java,resources} 
	mkdir -p src/test/{java,resources} 
	
	#生成web项目
	mkdir -p src/main/webapp
```
	
## 方法2：自定义task

### (1)创建一个testProject目录  
### (2)在testProject目录build.gradle：  

```
apply plugin: 'java'
apply plugin: 'war'

task createJavaProject << {
	sourceSets*.java.srcDirs*.each { it.mkdirs() }
	sourceSets*.resources.srcDirs*.each { it.mkdirs()}
}

task createWebProject(dependsOn: 'createJavaProject') << {
	def webAppDir = file("$webAppDirName")
	webAppDir.mkdirs()
}

```

### (3)命令行运行 

gradle createJavaProject  	=>java项目   
gradle createWebProject 	=>web项目  
 

```bash
xionglie@lie-mac:/tmp/testProject $ gradle createJavaProject
:createJavaProject

BUILD SUCCESSFUL

Total time: 3.31 secs
xionglie@lie-mac:/tmp/testProject $ tree
.
├── build.gradle
└── src
    ├── main
    │   ├── java
    │   └── resources
    └── test
        ├── java
        └── resources

7 directories, 1 file
xionglie@lie-mac:/tmp/testProject $ rm -rf src/
xionglie@lie-mac:/tmp/testProject $ gradle createWebProject
:createJavaProject
:createWebProject

BUILD SUCCESSFUL

Total time: 3.46 secs
xionglie@lie-mac:/tmp/testProject $ tree
.
├── build.gradle
└── src
    ├── main
    │   ├── java
    │   ├── resources
    │   └── webapp
    └── test
        ├── java
        └── resources

8 directories, 1 file
xionglie@lie-mac:/tmp/testProject $ 
```
