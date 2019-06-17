---

title: Build OpenJDK 10 Zero VM on macOS (interpreted mode)
date: 2018/04/17
tags: [Java,JDK,OpenJDK,ZeroVM,Hotspot]

---

## 前置

环境依赖:  

1. [安装一个 JDK9/10 (当然是越新越好啦🤐)](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
2. [Autogen](http://www.gnu.org/software/autogen)
2. [Autoconf](http://www.gnu.org/software/autoconf)
3. [Python 2](https://www.python.org/)
4. [Mercurial](https://www.mercurial-scm.org/)
5. [Libffi](https://github.com/libffi/libffi)
6. [Freetype](https://www.freetype.org)
6. [Ccache](https://ccache.samba.org)

[👉🏼 依赖安装](https://github.com/yakirChen/macOS-libs/blob/master/build.md)

## 获得源码

```bash
hg clone http://hg.openjdk.java.net/jdk-updates/jdk10u/
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
bash ./configure --with-debug-level=slowdebug \
    --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk-10.0.1.jdk/Contents/Home \
    --with-freetype=/Users/yakir/local \
    --with-freetype-include=/Users/yakir/local/include/freetype2 \
    --disable-warnings-as-errors \
    --with-jvm-features=zero \
    --with-jvm-variants=zero \
    --with-libffi=/Users/yakir/local/lib \
    --with-libffi-include=/Users/yakir/local/include \
    --with-num-cores=4 \
    --with-jobs=4
```
`--enable-ccache`选项看着加😁

## `configure` 成功输出
```bash
====================================================
A new configuration has been successfully created in
/Volumes/Toshiba/Developer/tools/jdk10u/build/macosx-x86_64-normal-zero-slowdebug
using configure arguments '--with-debug-level=slowdebug --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk-10.0.1.jdk/Contents/Home --with-freetype=/Users/yakir/local --with-freetype-include=/Users/yakir/local/include/freetype2 --disable-warnings-as-errors --with-jvm-features=zero --with-jvm-variants=zero --with-libffi=/Users/yakir/local/lib --with-libffi-include=/Users/yakir/local/include --with-num-cores=4 --with-jobs=4'.

Configuration summary:
* Debug level:    slowdebug
* HS debug level: debug
* JDK variant:    normal
* JVM variants:   zero
* OpenJDK target: OS: macosx, CPU architecture: x86, address length: 64
* Version string: 10.0.1-internal+0-adhoc.yakir.jdk10u (10.0.1-internal)

Tools summary:
* Boot JDK:       java version "10.0.1" 2018-04-17 Java(TM) SE Runtime Environment 18.3 (build 10.0.1+10) Java HotSpot(TM) 64-Bit Server VM 18.3 (build 10.0.1+10, mixed mode)  (at /Library/Java/JavaVirtualMachines/jdk-10.0.1.jdk/Contents/Home)
* Toolchain:      clang (clang/LLVM from Xcode 9.3)
* C Compiler:     Version 9.1.0 (at /usr/bin/clang)
* C++ Compiler:   Version 9.1.0 (at /usr/bin/clang++)

Build performance summary:
* Cores to use:   4
* Memory limit:   16384 MB

The following warnings were produced. Repeated here for convenience:
WARNING: Ignoring CFLAGS(-I/Users/yakir/local/include -I/Users/yakir/local/include/lzma -I/Users/yakir/local/include/openssl -I/Users/yakir/local/include/readline) found in environment. Use --with-extra-cflags
WARNING: Ignoring CXXFLAGS(-I/Users/yakir/local/include -I/Users/yakir/local/include/lzma -I/Users/yakir/local/include/openssl -I/Users/yakir/local/include/readline) found in environment. Use --with-extra-cxxflags
WARNING: Ignoring LDFLAGS(-L/Users/yakir/local/lib) found in environment. Use --with-extra-ldflags
```

## Make
```bash
make JOBS=4
```

+ 修改文件 `jdk10u/make/copy/Copy-java.base.gmk`
  ```bash
  vim +79 jdk10u/make/copy/Copy-java.base.gmk
  # 这行 JVMCFG_SRC := $(JDK_TOPDIR)/src/java.base/macosx/conf/$(JVMCFG_ARCH)/jvm.cfg
  # $(JVMCFG_ARCH) 修改为 x86_64
  # 修改之后
  # JVMCFG_SRC := $(JDK_TOPDIR)/src/java.base/macosx/conf/x86_64/jvm.cfg
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
  /Volumes/Toshiba/Developer/tools/jdk10u/make/hotspot/../../src/hotspot/os_cpu/bsd_zero/atomic_bsd_zero.hpp:193:37: error: cannot initialize a parameter of type 'char *' with an lvalue of type 'unsigned long'
    return __sync_add_and_fetch(dest, add_value);
                                      ^~~~~~~~~
     ... (rest of output omitted)
  ```
  修改
  ```bash
  vim +193 jdk10u/src/hotspot/os_cpu/bsd_zero/atomic_bsd_zero.hpp
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


