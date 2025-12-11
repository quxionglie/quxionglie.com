---
title: 容器环境下的java程序配置
date: 2023-11-01 21:23:02
tags:
categories:
comments: true
---


容器（Docker / Kubernetes）环境下运行 Java 程序确实经常出现问题，原因并不是 Java 自身不兼容容器，而是 JVM 在容器内对资源的默认认知与实际情况不一致，导致各种性能、内存、CPU 的异常。

如果不正确配置，会导致：
- JVM 默认按宿主机资源计算导致 OOM
- GC 压力变大
- 性能不稳定
- CPU 使用不符合预期：如错误判断了可获取的 CPU 资源，例如Docker 限制了 CPU 的核数，JVM 就可能设置不合适的 GC 并行线程数等。

# 1. 为什么 Go / Node / Rust 更适合现代架构？

如果考虑到微服务、Serverless 等新的架构和场景，Java 自身的大小、内存占用、启动速度，都存在一定局限性，因为 Java 早期的优化大多是针对长时间运行的大型服务器端应用。

现代云原生架构更喜欢轻量、快启动、小内存占用的语言。

| 语言      | 冷启动 | 内存占用 | 运行时大小       | 适用场景       |
|---------|-----|------|-------------|------------|
| Go      | 很快  | 小    | 小           | 微服务、云原生    |
| Node.js | 非常快 | 小    | 小           | Serverless |
| Rust    | 极快  | 极小   | 几乎无 runtime | 轻量服务、高性能   |
| Java    | 较慢  | 较大   | JVM-heavy   | 大型长期运行的服务  |

# 2. Docker与虚拟机

Docker 并不是一种完全的虚拟化技术，而更是一种轻量级的隔离技术。

从技术角度，基于 namespace，Docker 为每个容器提供了单独的命名空间，对网络、PID、用户、IPC 通信、文件系统挂载点等实现了隔离。对于 CPU、内存、磁盘 IO 等计算资源，则是通过 CGroup 进行管理。

Docker 仅在类似 Linux 内核之上实现了有限的隔离和虚拟化，并不是像传统虚拟化软件那样，独立运行一个新的操作系统。如果是虚拟化的操作系统，不管是 Java 还是其他程序，只要调用的是同一个系统 API，都可以透明地获取所需的信息，基本不需要额外的兼容性改变。

# 3. Java 在容器环境的局限性来源

## 3.1. Java 的设计初衷与容器化目标不同（最根本）

Java 1995–2010 年的发展主要针对：

- 大型、长期运行的服务端应用
- 几十GB内存的大进程
- 对吞吐要求高，对启动速度不敏感
- 在物理机或虚拟机上长期运行

优化方向包括：

- JIT（需要预热时间）
- 大型堆相关的 GC（G1、CMS、ZGC）
- 复杂类加载 / 反射框架
- 完整 JVM runtime（几十 MB）

容器出现后，需求完全改变了：

- 小内存（几百 MB～几 GB）
- 快速启动（Serverless 毫秒级）
- 高密度部署（1台机器上跑几百个容器）
- 弹性伸缩频繁重启（不希望预热）

Java 的设计初衷与这些“轻量化”目标自然有冲突。

## 3.2 JVM 天生的“大运行时”带来的资源开销

一个最小 Spring Boot 应用：
JVM 自身（20~40MB）
Metaspace（几十MB）
Thread stacks（默认 1MB/线程）
JIT code cache（几十MB）
Young/Old 堆空间（通常从几百 MB 起）
对于 512MB–1GB 容器，这些都是巨大开销

## 3.3. JVM 在容器中对 CPU/内存的错误认知

特别是 Java 8，默认：
以宿主机 CPU 数量决定 GC 线程开销
以宿主机内存计算默认 Xmx/Xms
ForkJoinPool 线程数按照宿主计算
JIT 后台线程也按宿主机数量创建

## 3.4. Java 复杂的 GC 机制不适合小内存容器

## 3.5. Java 冷启动慢，不适合 Serverless

Java 冷启动明显处于劣势：

| 技术                  | 冷启动速度        |
|---------------------|--------------|
| Java + Spring Boot  | 1〜5 秒（甚至更久）  |
| Java + Quarkus（JIT） | 0.5〜1.5 秒    |
| Go                  | 10〜50ms      |
| Node                | 10〜50ms      |
| Rust                | <10ms        |
| Java Native Image   | <50ms（但兼容性差） |

## 3.6. 非堆内存问题导致容器 OOM Kill

容器 OOMKill 的常见原因：
DirectBuffer（Netty）占用太大
Thread stacks 撑爆内存
Metaspace 不受 Xmx 控制
JIT code cache 填满
JVM 自身元数据占用

## 3.7. 安全隔离级别不同导致潜在安全隐患

JVM 最初预期运行在 VM/物理机中，不必担心：

- cgroup 调度方式
- namespace 隔离
- 容器逃逸可能导致的内核风险

而容器共享宿主机内核，JVM 程序没有进一步隔离：

- 不完全符合轻量级安全需求
- 没有沙箱级别隔离

# 4.容器环境下如何设置JAVA参数
历史
- JDK8u121加入了UseCGroupMemoryLimitForHeap这一参数，对容器内存设置做支持。(JDK-8170888)
- JDK8u191后加入了UseContainerSupport、MaxRAMPercentage、MinRAMPercentage、InitialRAMPercentage参数。deprecate了UseCGroupMemoryLimitForHeap、MaxRAMFraction、MinRAMFraction、InitialRAMFraction参数

### 4.1. 各JDK版本设置

(1) java版本<8u121
不要在容器化环境使用。

(2) 8u121<java版本<8u191
如果是使用OracleJDK需要额外开启实验参数 -XX:UnlockExperimentalVMOptions。

建议使用如下参数：
-XX:UseCGroupMemoryLimitForHeap -XX:MaxRAMFraction=2 -XX:MinRAMFraction=2
或自行设置

-Xmx:{用户自定义}

(3) java版本>=8u191
JDK8u191后新增了容器支持开关-XX:UseContainerSupport，并且默认开启。
并增加了这些参数：

MaxRAMPercentage 堆的最大值百分比。
InitialRAMPercentage 堆的初始化的百分比。
MinRAMPercentage 堆的最小值的百分比。

使用支持容器感知的 JVM 版本：
JDK 8u191+、JDK 10+ 默认支持容器 CPU 和内存感知；
通过参数 -XX:+UseContainerSupport 启用（JDK 10+ 默认启用）

注意：如果你可以切换到 JDK 10 或者更新的版本，问题就更加简单了。Java
对容器（Docker）的支持已经比较完善，默认就会自适应各种资源限制和实现差异。前面提到的实验性参数“UseCGroupMemoryLimitForHeap”已经被标记为废弃。

### 4.2. UseContainerSupport默认值

-XX:[+|-]UseContainerSupport

语法：
-XX:[+|-]UseContainerSupport

| Setting                  | Effect  | Default |
|--------------------------|---------|---------|
| -XX:-UseContainerSupport | Disable |         |
| -XX:+UseContainerSupport | Enable  | yes     |

-XX:+UseContainerSupport下表显示了设置该参数时使用的值：

| Container memory limit <size> | Maximum Java heap size |
|-------------------------------|------------------------|
| Less than 1 GB                | 50% <size>             |
| 1 GB - 2 GB                   | <size> - 512 MB        |
| Greater than 2 GB             | 75% <size>             |

默认堆大小上限为 25 GB，这是压缩引用 3 位移位运算的堆大小限制（参见-Xcompressedrefs 选项），以防止静默切换到压缩引用 4
位移位运算，因为这可能会降低性能。您可以使用-Xmx或-XX:MaxRAMPercentage选项来覆盖 25 GB 的限制。

容器的默认堆大小仅在满足以下条件时生效：

1. 该应用程序运行在容器环境中。
2. 容器的内存限制已设置。
3. 该-XX:+UseContainerSupport选项已设置，这是默认行为。
   为防止虚拟机在容器中运行时调整最大堆大小，请设置-XX:-UseContainerSupport。

当与-XX:MaxRAMPercentage`/`-XX:InitialRAMPercentage一起使用时-XX:
+UseContainerSupport，相应的堆设置将根据容器的内存限制确定。例如，要将最大堆大小设置为容器内存的 80%，请指定以下选项：

```
-XX:+UseContainerSupport -XX:MaxRAMPercentage=80
```

注意：如果为 设置了值-Xms，则该-XX:InitialRAMPercentage选项将被忽略。如果为 设置了值-Xmx，则该-XX:MaxRAMPercentage选项将被忽略。

## 4.3 建议
(1) 升级到最新的 JDK 版本。
(2) 明确启用 -XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0
(3) 在 K8s 生产环境运行 Java 程序时，一定要为容器明确限制 CPU（cpu limit）和内存（memory limit）。
```groovy
resources:
  requests:
    cpu: "1"
    memory: "2Gi"
  limits:
    cpu: "2"
    memory: "4Gi"
```
含义：
- request : 调度保证有足够资源
- limit : JVM 内看到的真实资源上限（尤其 memory）

对 JVM 的影响：
- JVM 会认为可用 CPU≈2（Java 10+ 自动感知）
- JVM 堆大小（Xmx）基于 4GB 计算（如用 -XX:MaxRAMPercentage）

### Dockerfile生产示例

```
Dockerfile
FROM openjdk:8-jdk-alpine

RUN echo http://mirrors.ustc.edu.cn/alpine/v3.9/main > /etc/apk/repositories \
&& echo http://mirrors.ustc.edu.cn/alpine/v3.9/community >> /etc/apk/repositories \
&& apk update \
&& apk add tzdata \
&& apk add curl \
&& apk add --update font-adobe-100dpi ttf-dejavu fontconfig \
&& cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
&& echo "Asia/Shanghai" > /etc/timezone && apk del tzdata \
&& mkdir /app \
&& rm -rf /var/cache/apk/*


ADD ./target/*.jar /app/app.jar
WORKDIR /app
EXPOSE 20003

ENTRYPOINT java -Dspring.profiles.active=$SPRING_ACTIVE  -XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0  -jar -Duser.timezone=Asia/Shanghai app.jar
```

# 5.参考资料
[容器环境下的相关JVM参数](https://jvm-argument-for-docker.teaho.net/)
[xx-usecontainersupport](https://www.ibm.com/docs/en/sdk-java-technology/8?topic=options-xx-usecontainersupport)
[杨晓峰 | Java程序运行在Docker等容器环境有哪些新问题？](https://time.geekbang.org/column/article/10975)

-- end -- 