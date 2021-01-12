---

title: Build OpenJDK 16 Zero VM on macOS (interpreted mode)
date: 2020/07/20
tags: [Java,JDK,OpenJDK,Zero VM,Hotspot]

---

## 前置

环境依赖:  

1. [安装一个 JDK14](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
2. [Autogen](http://www.gnu.org/software/autogen)
2. [Autoconf](http://www.gnu.org/software/autoconf)
3. [Python 2](https://www.python.org/)
5. [Libffi](https://github.com/libffi/libffi)
6. [Freetype](https://www.freetype.org)
7. [Ccache](https://ccache.samba.org)
8. [XCode Version 11.6 (11E708) macOS Version 10.15.6 (19G73)](https://developer.apple.com/download/more/)

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
    --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk-14.0.1.jdk/Contents/Home/ \
    --with-toolchain-path=/Volumes/sm/app/Xcode10.3.app/Contents/Developer/usr/bin \
    --disable-warnings-as-errors \
    --with-boot-jdk-jvmargs="-XX:+UseG1GC -Xms2G -Xmx2G" \
    --with-toolchain-type=clang \
    --with-debug-level=slowdebug \
    --with-native-debug-symbols=internal \
    --with-jvm-variants=zero \
    --with-target-bits=64 \
    --with-libffi=/Users/yakir/local/brew/opt/libffi \
    --with-num-cores=4 \
    --with-jobs=12
```
`--enable-ccache`选项看着加😁

## `configure` 成功输出
```bash
====================================================
A new configuration has been successfully created in
/Volumes/sm/jdk/jdkbuild/build/macosx-x86_64-zero-slowdebug
using configure arguments '--enable-dtrace --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk-14.0.2.jdk/Contents/Home/ --disable-warnings-as-errors --with-boot-jdk-jvmargs='-XX:+UseG1GC -Xms2G -Xmx2G' --with-toolchain-type=clang --with-debug-level=slowdebug --with-native-debug-symbols=internal --with-jvm-variants=zero --with-target-bits=64 --with-libffi=/Users/yakir/local/brew/opt/libffi'.

Configuration summary:
* Debug level:    slowdebug
* HS debug level: debug
* JVM variants:   zero
* JVM features:   zero: 'dtrace jni-check jvmti management nmt parallelgc serialgc services vm-structs zero' 
* OpenJDK target: OS: macosx, CPU architecture: x86, address length: 64
* Version string: 16-internal+0-adhoc.yakir.jdkbuild (16-internal)

Tools summary:
* Boot JDK:       java version "14.0.2" 2020-07-14 Java(TM) SE Runtime Environment (build 14.0.2+12-46) Java HotSpot(TM) 64-Bit Server VM (build 14.0.2+12-46, mixed mode, sharing)  (at /Library/Java/JavaVirtualMachines/jdk-14.0.2.jdk/Contents/Home)
* Toolchain:      clang (clang/LLVM from Xcode 11.6)
* C Compiler:     Version 11.0.3 (at /usr/bin/clang)
* C++ Compiler:   Version 11.0.3 (at /usr/bin/clang++)

Build performance summary:
* Cores to use:   12
* Memory limit:   32768 MB
```

## Make
```bash
make JOBS=12 && make images
```


## 验证

```bash
% time ./macosx-x86_64-zero-slowdebug/jdk/bin/java -version 
openjdk version "16-internal" 2021-03-16
OpenJDK Runtime Environment (slowdebug build 16-internal+0-adhoc.yakir.jdkbuild)
OpenJDK 64-Bit Zero VM (slowdebug build 16-internal+0-adhoc.yakir.jdkbuild, interpreted mode)
./macosx-x86_64-zero-slowdebug/jdk/bin/java -version  15.22s user 0.26s system 99% cpu 15.621 total
```

```bash
% time ./macosx-x86_64-zero-slowdebug/jdk/bin/javac -version
javac 16-internal
./macosx-x86_64-zero-slowdebug/jdk/bin/javac -version  20.36s user 0.32s system 99% cpu 20.839 total
```

```bash
% time ./macosx-x86_64-zero-slowdebug/jdk/bin/java /Volumes/sm/Hello.java 
Hello
./macosx-x86_64-zero-slowdebug/jdk/bin/java /Volumes/sm/Hello.java  81.46s user 2.10s system 99% cpu 1:24.18 total
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
