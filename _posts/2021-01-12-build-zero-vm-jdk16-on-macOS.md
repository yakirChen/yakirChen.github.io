---

title: Build OpenJDK 17 Zero VM on macOS (interpreted mode)
date: 2021/01/12
tags: [Java,JDK,OpenJDK,Zero VM,Hotspot]

---

## 前置

环境依赖:  

1. [安装一个 JDK15](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
2. [Autogen](http://www.gnu.org/software/autogen)
2. [Autoconf](http://www.gnu.org/software/autoconf)
3. [Python 2](https://www.python.org/)
5. [Libffi](https://github.com/libffi/libffi)
6. [Freetype](https://www.freetype.org)
7. [Ccache](https://ccache.samba.org)
8. [XCode Version 12.3 (12C33) macOS Version 11.1 (20C69)](https://developer.apple.com/download/more/)

[👉🏼 依赖安装](https://github.com/yakirChen/macOS-libs/blob/master/build.md)

## 获得源码

```bash
git clone --recurse-submodules --depth 1 https://github.com/openjdk/jdk.git
```

## libffi
```bash
git clone --depth 1 git@github.com:libffi/libffi.git
cd libffi
./autogen.sh && \
    python ./generate-darwin-source-and-headers.py && \
    ./configure --enable-debug --enable-purify-safety --prefix=${LOCAL_DIR} && \
    make -j4 && make install  
```

## 生成Makefile
```bash
make clean ; make dist-clean;
sh ./configure --enable-dtrace \
    --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk-15.0.1.jdk/Contents/Home/ \
    --with-toolchain-path=/Applications/Xcode.app/Contents/Developer/usr/bin \
    --disable-warnings-as-errors \
    --with-boot-jdk-jvmargs="-XX:+UseG1GC -Xms4G -Xmx4G" \
    --with-toolchain-type=clang \
    --with-debug-level=slowdebug \
    --with-native-debug-symbols=internal \
    --with-jvm-variants=zero \
    --with-target-bits=64 \
    --with-libffi=/Users/yakir/local/brew/opt/libffi \
    --with-num-cores=12 \
    --with-jobs=12
```
`--enable-ccache`选项看着加😁

## `configure` 成功输出
```bash
====================================================
A new configuration has been successfully created in
/Volumes/sm/github/jdk/jdkbuild/build/macosx-x86_64-zero-slowdebug
using configure arguments '--enable-dtrace --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk-15.0.1.jdk/Contents/Home/ --with-toolchain-path=/Applications/Xcode.app/Contents/Developer/usr/bin --disable-warnings-as-errors --with-boot-jdk-jvmargs='-XX:+UseG1GC -Xms4G -Xmx4G' --with-toolchain-type=clang --with-debug-level=slowdebug --with-native-debug-symbols=internal --with-jvm-variants=zero --with-target-bits=64 --with-libffi=/Users/yakir/local/brew/opt/libffi --with-num-cores=4 --with-jobs=12'.

Configuration summary:
* Name:           macosx-x86_64-zero-slowdebug
* Debug level:    slowdebug
* HS debug level: debug
* JVM variants:   zero
* JVM features:   zero: 'dtrace epsilongc g1gc jni-check jvmti management nmt parallelgc serialgc services shenandoahgc vm-structs zero' 
* OpenJDK target: OS: macosx, CPU architecture: x86, address length: 64
* Version string: 17-internal+0-adhoc.yakir.jdkbuild (17-internal)

Tools summary:
* Boot JDK:       java version "15.0.1" 2020-10-20 Java(TM) SE Runtime Environment (build 15.0.1+9-18) Java HotSpot(TM) 64-Bit Server VM (build 15.0.1+9-18, mixed mode, sharing) (at /Library/Java/JavaVirtualMachines/jdk-15.0.1.jdk/Contents/Home)
* Toolchain:      clang (clang/LLVM from Xcode 12.3)
* C Compiler:     Version 12.0.0 (at /usr/bin/clang)
* C++ Compiler:   Version 12.0.0 (at /usr/bin/clang++)

Build performance summary:
* Cores to use:   12
* Memory limit:   32768 MB
```

## Make
```bash
make JOBS=12 all && make images

# 增量编译hotspot
make hotspot
```


## 验证(进`build`目录)

```bash
% time ./macosx-x86_64-zero-slowdebug/jdk/bin/java -version 
openjdk version "17-internal" 2021-09-14
OpenJDK Runtime Environment (slowdebug build 17-internal+0-adhoc.yakir.jdkbuild)
OpenJDK 64-Bit Zero VM (slowdebug build 17-internal+0-adhoc.yakir.jdkbuild, interpreted mode)
./macosx-x86_64-zero-slowdebug/jdk/bin/java -version  4.32s user 0.19s system 93% cpu 4.850 total
```

```bash
% time ./macosx-x86_64-zero-slowdebug/jdk/bin/javac -version 
javac 17-internal
./macosx-x86_64-zero-slowdebug/jdk/bin/javac -version  6.75s user 0.22s system 77% cpu 8.975 total
```

```bash
% time ./macosx-x86_64-zero-slowdebug/jdk/bin/java /Volumes/sm/Hello.java
Hello
./macosx-x86_64-zero-slowdebug/jdk/bin/java /Volumes/sm/Hello.java  39.56s user 1.85s system 47% cpu 1:27.48 total
```

对比Oracle Java 11
```bash
% time java /Volumes/sm/Hello.java
Hello
java /Volumes/sm/Hello.java  2.08s user 0.21s system 184% cpu 1.242 total
```

对比OpenJDK Java 16
```bash
% time java /Volumes/sm/Hello.java
Hello
java /Volumes/sm/Hello.java  1.04s user 0.07s system 214% cpu 0.515 total
```



```java
// Hello.java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello");
    }
}
```
