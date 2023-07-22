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
git clone --recursive --depth 1 git@github.com:cisco/ChezScheme.git
gcr1 git@github.com:cisco/ChezScheme.git
```

## 2. 预先配置环境变量

```bash
export CHEZ_SCHEME=${LOCAL}/ChezScheme
export PATH=$CHEZ_SCHEME/bin:$PATH
```

## 3. 编译安装

```bash
./configure --disable-x11 \
    --threads \
    --installprefix=${CHEZ_SCHEME}
make -j 12
make install
```

## 4. 安装校验

```bash
# 版本号
scheme --version
# 9.5.9

# 运行
scheme
```

## 5. 退出

```scheme
(exit)
```

## 入门文档 (感谢 🤓

[Learning-SICP](https://github.com/DeathKing/Learning-SICP)  
[yast-cn](http://deathking.github.io/yast-cn)  
[维基教科书:Lisp 入门](https://zh.wikibooks.org/wiki/Lisp_入門)  
[ANSI Common Lisp 中文翻譯版](http://acl.readthedocs.io/en/latest/)  
[Scheme Note](http://irw.ncut.edu.tw/peterju/scheme.html)  
[Scheme语言简明教程](https://www.gitbook.com/read/book/wizardforcel/teach-yourself-scheme)  或者  [Scheme语言简明教程](http://songjinghe.github.io/TYS-zh-translation/)  
[MIT Scheme 的基本使用](https://www.math.pku.edu.cn/teachers/qiuzy/progtech/scheme/mit_scheme.htm)
[Chez Scheme Version 9 User's Guide](http://cisco.github.io/ChezScheme/csug9.5/csug.html)
