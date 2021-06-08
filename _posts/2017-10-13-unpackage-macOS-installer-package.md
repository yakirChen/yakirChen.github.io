---
title: macOS安装器解包
date: 2017/10/13
tags: [macOS]
categories: [Tech]
toc: true
description: macOS安装器解包
---

一个场景需要用到较低版本JVM(最终选择的是Java 6)。Oracle没有提供JDK6的安装包，Apple提供了JDK6的安装器，但是不想以Framework的形式安装(洁癖? orn)，所以想着解包自己放到自定义目录。

文中参考链接可以下到Apple包装的`JDK 1.6.0_65`，以下是解包步骤。

## 解包步骤

### 1. 挂载dmg镜像

### 2. 创建个目录用来存放解包之后的文件
```bash
mkdir pkg && cd pkg
```

### 3. 解包
```bash
xar -xf /Volumes/Java\ for\ OS\ X\ 2015-001/JavaForOSX.pkg
```
得到一个文件和四个目录，需要关注的是`JavaForOSX.pkg`这个目录
```
Distribution
JavaEssentials.pkg
JavaForOSX.pkg
JavaMDNS.pkg
Resources
```

### 4. 获得需要的资源
解包Payload文件
```bash
tar zxvf JavaForOSX.pkg/Payload\ Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents \
    -C ./
```
在当前目录生成资源文件目录结构是`Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents`,`Contents`包含的内容就是JAVA_HOME,  
```bash
# 执行拷贝
cp -R Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents ${LOCAL}/jdk6
```
之后就可以得到JDK6

### 5. 环境变量
配置环境变量的时候需要将`JAVA_6_HOME`指向`${LOCAL}/jdk6/Home`目录

```bash
% java -version
java version "1.6.0_65"
Java(TM) SE Runtime Environment (build 1.6.0_65-b14-468)
Java HotSpot(TM) 64-Bit Server VM (build 20.65-b04-468, mixed mode)
```

### by the way
无意中发现一款app `SuspiciousPackage`可以把这些个步骤可视化。  
打开app ›『File』› 『Open』› 选中镜像中的`JavaForOSX.pkg` › 选中 **`All Files`**tab浏览文件 › 选中`Contents` ›『File』› 『Export "Contents"』

_最后两步导出动作可由快捷键 『Shift+Commond+E』触发_


--- 

参考链接

[Apple JDK](https://support.apple.com/kb/DL1572?locale=zh_CN)  
[维基百科 **Xar存档**](https://en.wikipedia.org/wiki/Xar_%28archiver%29)  
[Stack Overflow **how-to-unpack-and-pack-pkg-file**](https://stackoverflow.com/questions/11298855/how-to-unpack-and-pack-pkg-file)
[SuspiciousPackage](http://www.mothersruin.com/software/SuspiciousPackage/)
