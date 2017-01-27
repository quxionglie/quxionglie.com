---
layout: post
title: "导入spring3.2源码到myeclipse中"
date: 2013-01-16 16:31
comments: true
categories: spring
---

spring3.2通过新的基于Gradle的构建来构建项目，它取代了之前的Ant+Ivy系统。
本文就介绍如何将spring3.2的源码导入到myeclipse中。

<!-- more -->

大伙可先参考一下spring的官方文档说明：https://github.com/SpringSource/spring-framework

```
Building from source  
The Spring Framework uses a Gradle-based build system. In the instructions below, ./gradlew is invoked from the root of the source tree and serves as a cross-platform, self-contained bootstrap mechanism for the build. The only prerequisites are Git and JDK 1.7+.（先决条件是Git和JDK 1.7+）  
check out sources  
	git clone git://github.com/SpringSource/spring-framework.git
 
compile and test, build all jars, distribution zips and docs    
	./gradlew build
 
install all spring-* jars into your local Maven cache  
	./gradlew install
import sources into your IDE  
Run ./import-into-eclipse.sh or read import-into-idea.md as appropriate.  
```

###本人电脑环境：mac OS X 10.8.2，jdk1.7.0_11,git 1.7.10.2

``` bash
lie-mac:spring xionglie$ java -version
java version "1.7.0_11"
Java(TM) SE Runtime Environment (build 1.7.0_11-b21)
Java HotSpot(TM) 64-Bit Server VM (build 23.6-b04, mixed mode)
lie-mac:spring xionglie$ git --version
git version 1.7.10.2 (Apple Git-33)

操作过程：
lie-mac:~ xionglie$ cd ~/Downloads/spring/
lie-mac:spring xionglie$ git clone git://github.com/SpringSource/spring-framework.git
lie-mac:spring xionglie$ cd spring-framework
lie-mac:spring-framework xionglie$ ./import-into-eclipse.sh 
```

注意：   
（1）.这里会执行5步操作，在第2步和第4步都要各导入(import)全部项目(~/Downloads/spring/spring-framework)到myeclipse中，相当于要导入两次，你只单独导入第4步的项目是不行的。    
（2）STEP 1的时间会比较长，如果执行不下去，请多次执行 ./import-into-eclipse.sh ；    
（3）myeclipse的编译环境要修改为jdk7,不要使用默认的jdk6。如我的jdk7的ire-home为 ：/Library/Java/JavaVirtualMachines/jdk1.7.0_11.jdk/Contents/Home        

Spring Framework Eclipse/STS project import guide
This script will guide you through the process of importing the
Spring Framework sources into Eclipse/STS. It is recommended that you
have a recent version of the SpringSource Tool Suite (this script has
been tested against STS 2.9.2.RELEASE), but at the minimum you will
need Eclipse + AJDT.  
If you need to download and install STS, please do that now by
visiting http://springsource.org/downloads/sts
Otherwise, press enter and we'll begin. (按enter)  
 
 
## STEP 1: Generate subproject Eclipse metadata
The first step will be to generate Eclipse project metadata for each  
of the spring-* subprojects. This happens via the built-in   
"Gradle wrapper" script (./gradlew in this directory). If this is your  
first time using the Gradle wrapper, this step may take a few minutes  
while a Gradle distribution is downloaded for you.  
The command run will be:

```
./gradlew cleanEclipse :spring-oxm:compileTestJava eclipse -x :eclipse
Press enter when ready. (按enter)
:buildSrc:compileJava UP-TO-DATE
:buildSrc:compileGroovy UP-TO-DATE
:buildSrc:processResources UP-TO-DATE

…省略….
BUILD SUCCESSFUL


Total time: 10 mins 30.351 secs
```

## STEP 2: Import subprojects into Eclipse/STS
Within Eclipse/STS, do the following:
	File > Import... > Existing Projects into Workspace
\>When prompted for the 'root directory', provide /Users/xionglie/Downloads/spring/spring-framework    
\> Press enter. You will see the modules show up under "Projects"  
\> All projects should be selected/checked. Click Finish.   
\> When the project import is complete, you should have no errors.    
When the above is complete, return here and press the enter key.   

## STEP 3: generate root project Eclipse metadata
Unfortunately, Eclipse does not allow for importing project  
hierarchies, so we had to skip root project metadata generation in the  
during step 1. In this step we simply generate root project metadata  
so you can import it in the next step.
The command run will be:

```
./gradlew :eclipse
Press the enter key when ready.
:buildSrc:compileJava UP-TO-DATE
:buildSrc:compileGroovy UP-TO-DATE
:buildSrc:processResources UP-TO-DATE
:buildSrc:classes UP-TO-DATE
:buildSrc:jar UP-TO-DATE
:buildSrc:assemble UP-TO-DATE
:buildSrc:compileTestJava UP-TO-DATE
:buildSrc:compileTestGroovy UP-TO-DATE
:buildSrc:processTestResources UP-TO-DATE
:buildSrc:testClasses UP-TO-DATE
:buildSrc:test UP-TO-DATE
:buildSrc:check UP-TO-DATE
:buildSrc:build UP-TO-DATE
:eclipseClasspath
:eclipseJdtPrepare
:eclipseJdt
:eclipseProject
:eclipseSettings
:eclipseWstComponent
:eclipse
BUILD SUCCESSFUL
Total time: 16.551 secs
```
## STEP 4: Import root project into Eclipse/STS
Follow the project import steps listed in step 2 above to import the
root project.   
Press enter when complete, and move on to the final step.

## STEP 5: Enable Git support for all projects
- In the Eclipse/STS Package Explorer, select all spring* projects.
- Right-click to open the context menu and select Team > Share Project…
- In the Share Project dialog that appears, select Git and press Next
- Check "Use or create repository in parent folder of project"
- Click Finish
When complete, you'll have Git support enabled for all projects.  
You're ready to code! Goodbye!

## 最终导入结果：
![Alt text](/images/2013/2013-01-16-import-spring-source-code-into-myeclipse.png)


