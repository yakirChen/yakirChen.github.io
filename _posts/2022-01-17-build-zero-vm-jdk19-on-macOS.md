---

title: Build OpenJDK 19 Zero VM on macOS (interpreted mode)
date: 2022/01/17
tags: [Java,JDK,OpenJDK,Zero VM,Hotspot]

---

## 前置

环境依赖:  

1. [安装一个 JDK17](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
2. [Autogen](http://www.gnu.org/software/autogen)
3. [Autoconf](http://www.gnu.org/software/autoconf)
4. [Python 2](https://www.python.org/)
5. [Libffi](https://github.com/libffi/libffi)
6. [Freetype](https://www.freetype.org)
7. [Ccache](https://ccache.samba.org)
8. [XCode Version 13.2.1 (13C100) macOS Version 12.1 (21C52)](https://developer.apple.com/download/more/)
9. [Binutils]( https://ftp.gnu.org/gnu/binutils/)

[👉🏼 依赖安装](https://github.com/yakirChen/macOS-libs/blob/master/build.md)

## 获得源码

```shell
git clone --recurse-submodules --depth 1 https://github.com/openjdk/jdk.git
```

## libffi

```shell
git clone --depth 1 git@github.com:libffi/libffi.git
cd libffi
./autogen.sh && \
    python ./generate-darwin-source-and-headers.py && \
    ./configure --enable-debug --enable-purify-safety --prefix=${LOCAL_DIR} && \
    make -j4 && make install  
```

## 生成Makefile

### Zero Slowdebug

```shell
make clean ; make dist-clean;
sh ./configure --enable-dtrace \
    --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk-17.0.1.jdk/Contents/Home/ \
    --with-toolchain-path=/Applications/Xcode.app/Contents/Developer/usr/bin \
    --disable-warnings-as-errors \
    --with-boot-jdk-jvmargs="-XX:+UseG1GC -Xms8G -Xmx8G" \
    --with-toolchain-type=clang \
    --with-debug-level=slowdebug \
    --with-native-debug-symbols=internal \
    --with-jvm-variants=zero \
    --with-target-bits=64 \
    --with-libffi=/Users/yakir/local/brew/opt/libffi \
    --with-num-cores=12 \
    --with-jobs=12
```

### Server & Client Fastdebug

```bash
make clean ; make dist-clean;
sh ./configure --enable-dtrace \
    --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk-17.0.1.jdk/Contents/Home/ \
    --with-toolchain-path=/Applications/Xcode.app/Contents/Developer/usr/bin \
    --disable-warnings-as-errors \
    --with-boot-jdk-jvmargs="-XX:+UseG1GC -Xms8G -Xmx8G" \
    --with-toolchain-type=clang \
    --with-debug-level=slowdebug \
    --with-native-debug-symbols=internal \
    --with-jvm-variants=server,client \
    --with-target-bits=64 \
    --with-libffi=/Users/yakir/local/brew/opt/libffi \
    --with-num-cores=12 \
    --with-jobs=12
```

`--enable-ccache`选项看着加😁

## Zero Slowdebug `configure` 成功输出

```shell
====================================================
A new configuration has been successfully created in
/Volumes/sm/github/jdk/jdkbuild/build/macosx-x86_64-zero-slowdebug
using configure arguments '--enable-dtrace --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk-17.0.1.jdk/Contents/Home/ --with-toolchain-path=/Applications/Xcode.app/Contents/Developer/usr/bin --disable-warnings-as-errors --with-boot-jdk-jvmargs='-XX:+UseG1GC -Xms8G -Xmx8G' --with-toolchain-type=clang --with-debug-level=slowdebug --with-native-debug-symbols=internal --with-jvm-variants=zero --with-target-bits=64 --with-libffi=/Users/yakir/local/brew/opt/libffi --with-num-cores=12 --with-jobs=12'.

Configuration summary:
* Name:           macosx-x86_64-zero-slowdebug
* Debug level:    slowdebug
* HS debug level: debug
* JVM variants:   zero
* JVM features:   zero: 'dtrace epsilongc g1gc jni-check jvmti management nmt parallelgc serialgc services shenandoahgc vm-structs zero' 
* OpenJDK target: OS: macosx, CPU architecture: x86, address length: 64
* Version string: 19-internal+0-adhoc.yakir.jdkbuild (19-internal)

Tools summary:
* Boot JDK:       java version "17.0.1" 2021-10-19 LTS Java(TM) SE Runtime Environment (build 17.0.1+12-LTS-39) Java HotSpot(TM) 64-Bit Server VM (build 17.0.1+12-LTS-39, mixed mode, sharing) (at /Library/Java/JavaVirtualMachines/jdk-17.0.1.jdk/Contents/Home)
* Toolchain:      clang (clang/LLVM from Xcode 13.2.1)
* C Compiler:     Version 13.0.0 (at /usr/bin/clang)
* C++ Compiler:   Version 13.0.0 (at /usr/bin/clang++)

Build performance summary:
* Build jobs:     12
* Memory limit:   32768 MB
```

## Server & Client Fastdebug `configure` 成功输出

```shell
====================================================
A new configuration has been successfully created in
/Volumes/sm/github/jdk/jdkbuild/build/macosx-x86_64-serverANDclient-fastdebug
using configure arguments '--enable-dtrace --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk-16.0.2.jdk/Contents/Home/ --with-toolchain-path=/Applications/Xcode.app/Contents/Developer/usr/bin --disable-warnings-as-errors --with-boot-jdk-jvmargs='-XX:+UseG1GC -Xms8G -Xmx8G' --with-toolchain-type=clang --with-debug-level=fastdebug --with-native-debug-symbols=internal --with-jvm-variants=server,client --with-target-bits=64 --with-libffi=/Users/yakir/local/brew/opt/libffi --with-num-cores=12 --with-jobs=12'.

Configuration summary:
* Name:           macosx-x86_64-serverANDclient-fastdebug
* Debug level:    fastdebug
* HS debug level: fastdebug
* JVM variants:   server client
* JVM features:   server: 'cds compiler1 compiler2 dtrace epsilongc g1gc jfr jni-check jvmci jvmti management nmt parallelgc serialgc services shenandoahgc vm-structs zgc' client: 'cds compiler1 dtrace epsilongc g1gc jfr jni-check jvmti management nmt parallelgc serialgc services shenandoahgc vm-structs zgc'
* OpenJDK target: OS: macosx, CPU architecture: x86, address length: 64
* Version string: 18-internal+0-adhoc.yakir.jdkbuild (18-internal)

Tools summary:
* Boot JDK:       java version "16.0.2" 2021-07-20 Java(TM) SE Runtime Environment (build 16.0.2+7-67) Java HotSpot(TM) 64-Bit Server VM (build 16.0.2+7-67, mixed mode, sharing) (at /Library/Java/JavaVirtualMachines/jdk-16.0.2.jdk/Contents/Home)
* Toolchain:      clang (clang/LLVM from Xcode 12.5.1)
* C Compiler:     Version 12.0.5 (at /usr/bin/clang)
* C++ Compiler:   Version 12.0.5 (at /usr/bin/clang++)

Build performance summary:
* Build jobs:     12
* Memory limit:   32768 MB

NOTE: You have requested to build more than one version of the JVM, which
will result in longer build times.

The following warnings were produced. Repeated here for convenience:
WARNING: libffi not used, so --with-libffi[-*] is ignored
```

## Make

```bash
make JOBS=12 all && make images

# 增量编译hotspot
make hotspot
```

## 验证(进`build`目录)

```shell
% time /Users/yakir/local/jdk/jdk-17.jdk/Contents/Home/bin/java -version 
openjdk version "17-internal" 2021-09-14
OpenJDK Runtime Environment (slowdebug build 17-internal+0-adhoc.yakir.jdkbuild)
OpenJDK 64-Bit Zero VM (slowdebug build 17-internal+0-adhoc.yakir.jdkbuild, interpreted mode)
/Users/yakir/local/jdk/jdk-17.jdk/Contents/Home/bin/java -version  0.61s user 0.02s system 22% cpu 2.800 total
```

```shell
% time /Users/yakir/local/jdk/jdk-17.jdk/Contents/Home/bin/javac -version 
javac 17-internal
/Users/yakir/local/jdk/jdk-17.jdk/Contents/Home/bin/javac -version  3.71s user 0.04s system 99% cpu 3.766 total
```

```shell
% time /Users/yakir/local/jdk/jdk-17.jdk/Contents/Home/bin/java /Volumes/sm/Hello.java 
Hello
/Users/yakir/local/jdk/jdk-17.jdk/Contents/Home/bin/java   20.35s user 0.10s system 99% cpu 20.497 total
```

对比Oracle Java 11

```shell
% time java /Volumes/sm/Hello.java 
Hello
java /Volumes/sm/Hello.java  1.00s user 0.12s system 108% cpu 1.032 total
```

对比OpenJDK Java 17

```shell
% time java /Volumes/sm/Hello.java 
Hello
java /Volumes/sm/Hello.java  0.89s user 0.07s system 219% cpu 0.439 total
```

```java
// Hello.java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello");
    }
}
```

## hsdis

```shell
cd src/utils/hsdis && 
    make ARCH=amd64 BINUTILS=binutils-2.34 CFLAGS="-Wno-error=logical-not-parentheses -Wno-error=string-plus-int -fbracket-depth=512" && \
    cp build/macosx-amd64/hsdis-amd64.dylib <JDK Home>/Contents/Home/lib/server/
```

使用: `java -XX:+UnlockDiagnosticVMOptions -XX:+PrintAssembly`

## IdealGraphVisualizer


## Clion 调试

```bash
make compile-commands
make compile-commands-hotspot
sh bin/idea.sh
```
生成`compile_commands.json`文件, 位于`${SRC}/build/macosx-x86_64-zero-slowdebug/`目录中

Clion `Open` `compile_commands.json`文件

选择 `open as project`




## References

[Tips & Tricks: Develop OpenJDK in CLion with Pleasure](https://blog.jetbrains.com/clion/2020/03/openjdk-with-clion/)
