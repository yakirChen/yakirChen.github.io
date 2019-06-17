---
title: Install Static Code Analysis Tool Infer(Facebook) 
date: 2018/06/01
tags: [macOS,Tech]
categories: [Tech]
toc: true
description: Install Facebook's Static Code Analysis Tool (Infer)
---

## OCaml 环境

### 预备
安装 **OCaml**环境, 我用 **OPAM**. 使用 **OPAM** 的原因当然是多版本管理啦😁,
我在安装前预先配置一个环境变量 `export OPAMROOT=/Volumes/sd/repos/opam`, 用于
存放`OPAM`安装的包、配置 ...  

安装 **darcs**(看介绍是个版本管理工具😑)
[http://darcs.net/Binaries](http://darcs.net/Binaries)  
下载来是个二进制文件, cp 到 bin 目录即可

### 安装 OPAM

选用了**1.2.2**版本(貌似要出**2.0.0**了)  

下载`OPAM`  
```bash
curl -L \
    https://github.com/ocaml/opam/releases/download/1.2.2/opam-1.2.2-x86_64-Darwin \
    -o ${LOCAL}/bin
```

验证  
```bash
opam --version
# 1.2.2
```

### 安装 OCaml

```bash
# 初始化 opam, 初始化的内容存放在 ${OPAMROOT} 中
opam init --comp=4.06.1+flambda
# 安装编译infer需要使用到的组件
opam install ocamlbuild
opam install menhir
opam install atdgen
opam install camlzip
opam install oUnit
opam install javalib
opam install sawja
```

### 获得 _**infer**_ 安装源码
[https://github.com/facebook/infer/releases](https://github.com/facebook/infer/releases)

[点击下载 `infer-osx-v0.14.0.tar.xz`](https://github.com/facebook/infer/releases/download/v0.14.0/infer-osx-v0.14.0.tar.xz)

```bash
tar Jxvf infer-osx-v0.14.0.tar.xz
./configure --prefix=${LOCAL}/infer && \
    make -j4 && \
    make install
# 漫长的等待 ... 编译这么久... 打开方式不对 😮
```

使用详解 👉 [http://fbinfer.com/](http://fbinfer.com/)
