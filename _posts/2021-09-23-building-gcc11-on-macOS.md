---
title: Building GCC 11 on macOS
date: 2021/09/23
tags: [GCC,cpp]
categories: [Tech]
toc: true
description: Building GCC 11 on macOS
---

## 概述

macOS Big Sur

OS: macOS 11.6 20G165 x86_64
Host: MacBookPro15,1
Kernel: 20.6.0

构建 GCC 11.2.0

## macOS命令行工具

```bash
xcode-select --install
```

## GCC套件和依赖

## 源包Download

+ [GCC源码安装包](https://bigsearcher.com/mirrors/gcc/releases)
  [点击下载`gcc-11.2.0.tar.xz`](https://bigsearcher.com/mirrors/gcc/releases/gcc-11.2.0/gcc-11.2.0.tar.xz)

+ [依赖包mpc](http://www.multiprecision.org/mpc/download.html)
  [点击下载`mpc-1.0.3.tar.gz`](ftp://ftp.gnu.org/gnu/mpc/mpc-1.0.3.tar.gz)

+ [依赖包mpfr](http://www.mpfr.org/mpfr-current/#download)
  [点击下载`mpfr-3.1.6.tar.gz`](http://www.mpfr.org/mpfr-current/mpfr-3.1.6.tar.gz)

+ [依赖包gmp](https://gmplib.org)
  [点击下载`gmp-6.1.2.tar.bz2`](https://gmplib.org/download/gmp/gmp-6.1.2.tar.bz2)

+ [依赖包isl](https://gcc.gnu.org/pub/gcc/infrastructure/)
  [点击下载`isl-0.18.tar.bz2`](https://gcc.gnu.org/pub/gcc/infrastructure/isl-0.18.tar.bz2)

## Unpackage & Building

```bash
export SOURCE_DIR=/Volumes/sm/build/gcc11
export GCC_HOME=/Users/yakir/local/gcc
export GCC_VERSION="11.2.0"
export GMP_VERSION="6.1.2"
export MPFR_VERSION="3.1.6"
export MPC_VERSION="1.0.3"
export ISL_VERSION="0.18"


# gmp
tar -jxvf gmp-${GMP_VERSION}.tar.bz2 && \
cd ${SOURCE_DIR}/gmp-${GMP_VERSION} && \
mkdir build && cd build && \
  ../configure --prefix=${GCC_HOME} --enable-cxx && \
  make -j4 && \
  make install && \
  cd ../../

# mpfr
tar -zxvf mpfr-${MPFR_VERSION}.tar.gz && \
cd ${SOURCE_DIR}/mpfr-${MPFR_VERSION} && \
mkdir build && cd build && \
  ../configure --prefix=${GCC_HOME} --with-gmp=${GCC_HOME} && \
  make -j4 && \
  make install && \
  cd ../../

# mpc
tar -zxvf mpc-${MPC_VERSION}.tar.gz && \
cd ${SOURCE_DIR}/mpc-${MPC_VERSION} && \
mkdir build && cd build && \
  ../configure --prefix=${GCC_HOME} \
  --with-gmp=${GCC_HOME} \
  --with-mpfr=${GCC_HOME} && \
  make -j4 && \
  make install && \
  cd ../../

# isl
tar -jxvf isl-${ISL_VERSION}.tar.bz2 && \
  cd ${SOURCE_DIR}/isl-${ISL_VERSION} && \
  mkdir build && cd build && \
  ../configure --prefix=${GCC_HOME} --with-gmp-prefix=${GCC_HOME} && \
  make -j4 && \
  make install && \
  cd ../../

# gcc
tar -Jxvf gcc-${GCC_VERSION}.tar.xz && \
  cd ${SOURCE_DIR}/gcc-${GCC_VERSION} && \
  mkdir build && cd build && \
  ../configure --prefix=${GCC_HOME} \
  --enable-checking=release \
  --disable-nls \
  --enable-host-shared \
  --enable-compressed-debug-sections=all \
  --enable-bootstrap \
  --with-gmp=${GCC_HOME} \
  --with-mpfr=${GCC_HOME} \
  --with-mpc=${GCC_HOME} \
  --enable-languages=c,c++,fortran,lto \
  --enable-host-shared \
  --with-isl=${GCC_HOME} \
  --disable-multilib \
  --build=x86_64-apple-darwin20.6.0 \
  --with-native-system-header-dir=/usr/include \
  --with-sysroot=/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk \
  --with-system-zlib && \
  make -j12 BOOT_LDFLAGS=-Wl,-headerpad_max_install_names && \
  make install
```


## GCC套件环境变量配置

配置环境变量

```bash
export GCC_HOME=$SERVER_BASE_HOME/gcc
PATH=${GCC_HOME}/bin:$PATH
```

## g++测试

```bash
% g++7 --version
g++7 (GCC) 7.3.0
Copyright © 2017 Free Software Foundation, Inc.
本程序是自由软件；请参看源代码的版权声明。本软件没有任何担保；
包括没有适销性和某一专用目的下的适用性担保。

% gcc7 --version
gcc7 (GCC) 7.3.0
Copyright © 2017 Free Software Foundation, Inc.
本程序是自由软件；请参看源代码的版权声明。本软件没有任何担保；
包括没有适销性和某一专用目的下的适用性担保。
```

```cpp
#include <iostream>
using namespace std;
int main(){
    for (int i = 0; i < 5; ++i){
        cout << "Hello GCC 7.3 ! count " << i << endl;
    }
    return 0;
}
/* 编译 & 运行 & 输出
% g++7 test.cpp -o test
% ./test 
Hello GCC 7.3 ! count 0
Hello GCC 7.3 ! count 1
Hello GCC 7.3 ! count 2
Hello GCC 7.3 ! count 3
Hello GCC 7.3 ! count 4*/
```

##### change_log

- Upgrade gcc 11.2

___

参考:

[Compiling GCC 7 on macOS](https://solarianprogrammer.com/2017/05/21/compiling-gcc-macos/)
