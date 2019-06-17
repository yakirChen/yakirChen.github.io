---
layout: post
title: Build OpenJDK11 Zero VM on macOS (interpreted mode) [待更新]
date: 2018/03/13
tags: [Java,JDK,OpenJDK,ZeroVM,Hotspot]
categories: [Tech]
toc: true
description: Build OpenJDK11 Zero VM on macOS (interpreted mode)
---

## 前置

环境依赖:  

1. [安装一个 JDK8 (当然是越新越好啦🤐)](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
2. [Autogen](http://www.gnu.org/software/autogen)
2. [Autoconf](http://www.gnu.org/software/autoconf)
3. [Python 2](https://www.python.org/)
4. [Mercurial](https://www.mercurial-scm.org/)
5. [Libffi](https://github.com/libffi/libffi)
6. [Freetype](https://www.freetype.org)

[👉🏼 依赖安装](https://github.com/yakirChen/macOS-libs/blob/master/build.md)

## 获得源码

```bash
hg clone http://hg.openjdk.java.net/jdk-updates/jdk11u/
```

## 安装依赖
### freetype
```bash
./configure --prefix=${LOCAL} --without-harfbuzz && \
    make -j4 && \
    make install
```

### libffi
```bash
git clone --depth 1 git@github.com:libffi/libffi.git
cd libffi
./autogen.sh && \
# python ./generate-darwin-source-and-headers.py && \
./configure --enable-debug \
    --disable-dependency-tracking \
    --enable-purify-safety \
    --prefix=${LOCAL} && \
    make -j4 && \
    make install
```

### ccache(optional)
```bash
./configure --prefix=${LOCAL} && \
    make -j4 && \
    make install
```

## 生成Makefile
```bash
mkdir build && \
cd build && \
bash ../configure --with-debug-level=slowdebug \
    --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk1.8.0_192.jdk/Contents/Home \
    --with-freetype=/Users/yakir \
    --with-freetype-include=/Users/yakir/local/include \
    --disable-warnings-as-errors \
    --with-jvm-features=zero \
    --with-jvm-variants=zero \
    --with-libffi=/Users/yakir/local \
    --with-libffi-include=/Users/yakir/local/include
```

## Troubleshooting
+  Error NO. 1
  ```bash
  configure: Found freetype include files at /Users/yakir/local/include/freetype2 using --with-freetype
  configure: Could not find ~/local/lib/libfreetype.dylib. Ignoring location.
  configure: error: Can not find or use freetype at location given by --with-freetype
  /Volumes/Toshiba/Developer/jdk/jdk9u/common/autoconf/generated-configure.sh: line 82: 5: Bad file descriptor
  configure exiting with result code 1
  ```

  配置 `freetype` 如果依赖安装在用户目录下(比如我的 `freetype` 安装在 `/Users/yakir/local` 路径下)需要使用完整路径(`/Users/yakir`)不能使用 `~` .

+ Error NO. 2
  ```bash
  Building target 'all' in configuration 'macosx-x86_64-normal-zero-slowdebug'
  Compiling 8 files for BUILD_TOOLS_LANGTOOLS
  Compiling 2 files for BUILD_JVMTI_TOOLS
  make[3]: *** No rule to make target '/Volumes/sd/Developer/jdk9u/jdk/src/java.base/macosx/conf/zero/jvm.cfg', needed by '/Volumes/sd/Developer/jdk9u/build/macosx-x86_64-normal-zero-slowdebug/support/modules_libs/java.base/jvm.cfg'.  Stop.
  make[3]: *** Waiting for unfinished jobs....
  make[2]: *** [java.base-copy-jdk] Error 2
  make[2]: *** Waiting for unfinished jobs....
  ERROR: Build failed for target 'all' in configuration 'macosx-x86_64-normal-zero-slowdebug' (exit code 2) 
  No indication of failed target found.
  Hint: Try searching the build log for '] Error'.
  Hint: See common/doc/building.html#troubleshooting for assistance.
  make[1]: *** [main] Error 2
  make: *** [all] Error 2
  ```

  修改文件 `jdk9u/jdk/make/copy/Copy-java.base.gmk`
  ```bash
  vim +97 jdk9u/jdk/make/copy/Copy-java.base.gmk  
  # 这行 JVMCFG_SRC := $(JDK_TOPDIR)/src/java.base/macosx/conf/$(JVMCFG_ARCH)/jvm.cfg
  # $(JVMCFG_ARCH) 修改为 x86_64
  # 修改之后
  # JVMCFG_SRC := $(JDK_TOPDIR)/src/java.base/macosx/conf/x86_64/jvm.cfg
  ```

+ Error NO. 3
  ```bash
  * For target hotspot_variant-zero_libjvm_objs_virtualspace.o:
  /Volumes/Toshiba/Developer/jdk/jdk9u/hotspot/src/share/vm/memory/virtualspace.cpp:584:14: error: ordered comparison between pointer and zero ('char *' and 'int')
    if (base() > 0) {
        ~~~~~~ ^ ~
  1 error generated.
  * All command lines available in /Volumes/Toshiba/Developer/jdk/jdk9u/build/macosx-x86_64-normal-zero-slowdebug/make-support/failure-logs.
  ```

  修改
  ```bash
  vim +584 jdk9u/hotspot/src/share/vm/memory/virtualspace.cpp
  把
  if (base() > 0)
  # 改成
  if (base() > (void *) 0)
  ```

+ ~~Error No. 4~~
  ```bash
  hotspot/src/share/vm/opto/lcm.cpp:42:35: error: ordered comparison between pointer and zero ('address' (aka 'unsigned char *') and 'int')
  if (Universe::narrow_oop_base() > 0) { // Implies UseCompressedOops.~~
      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ ^ ~
  1 error generated.
  ```
  补丁在此: [8174050: Compilation errors with clang-4.0](http://hg.openjdk.java.net/jdk10/jdk10/hotspot/rev/316854ef2fa2)
  貌似没这个Error了

## 验证

```bash
% java -version 
openjdk version "9.0.4-internal"
OpenJDK Runtime Environment (slowdebug build 9.0.4-internal+0-adhoc.yakir.jdk9u)
OpenJDK 64-Bit Zero VM (slowdebug build 9.0.4-internal+0-adhoc.yakir.jdk9u, interpreted mode)

% javac -version
javac 9.0.4-internal
```


## Update

- 2018/10/26 OpenJDK9 -> OpenJDK11
