---
title: Chez Scheme安装初探(macOS)
date: 2017/06/26
tags: [Scheme,Lisp,macOS]
categories: [Tech]
toc: true
description: Chez Scheme安装初探(macOS)
---


## 1. 获取代码
```bash
git clone --recursive git@github.com:cisco/ChezScheme.git
```


## 2. 预先配置环境变量
```bash
export CHEZ_SCHEME_HOME=${SERVER_BASE_HOME}/chez_scheme
export PATH=$CHEZ_SCHEME_HOME/bin:$PATH
```


## 3. 编译安装
```bash
./configure --installprefix=${CHEZ_SCHEME_HOME}
make -j4
make install
```


## 4. macOS X11问题
macOS在没有X11的情况下，在make阶段会报找不到头文件 (摊手
```bash
expeditor.c:886:10: fatal error: 'X11/Xlib.h' file not found
#include <X11/Xlib.h>
         ^
1 error generated.
```

解决: 注释引用头文件的代码
文件路径 ChezScheme/c/version.h
代码大约在283行 (vim下输`283G`
```c
#define LIBX11 "/usr/X11R6/lib/libX11.dylib"
// 变为
// #define LIBX11 "/usr/X11R6/lib/libX11.dylib"
```


## 5. 安装校验 
```bash
# 版本号
scheme --version
# 现在代码编译版本是 9.4.1
# 17-10/12更新到 9.5.1
# 运行
scheme
```


## 6. 退出
```scheme
(exit)
```


## 入门文档 (感谢 🤓

[yast-cn](http://deathking.github.io/yast-cn)  
[维基教科书:Lisp 入门](https://zh.wikibooks.org/wiki/Lisp_入門)  
[ANSI Common Lisp 中文翻譯版](http://acl.readthedocs.io/en/latest/)  
[Scheme Note](http://irw.ncut.edu.tw/peterju/scheme.html)  
[Scheme语言简明教程](https://www.gitbook.com/read/book/wizardforcel/teach-yourself-scheme)  
或者  
[Scheme语言简明教程](http://songjinghe.github.io/TYS-zh-translation/)  
