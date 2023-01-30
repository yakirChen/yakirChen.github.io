---
title: Squirrel构建安装(macOS)
date: 2016/11/20
tags: [Rime,Squirrel,macOS]
categories: [Tech]
toc: true
description: Squirrel构建安装(macOS)
---

## 预览

1). **黑色主题**

![rime_black_new.png](/imgs/squirrel-screenshot/rime_black_new.png)

2). **白色主题**

![rime_white.png](/imgs/squirrel-screenshot/rime_white.png)

![rime_white_placeless.png](/imgs/squirrel-screenshot/rime_white_placeless.png)

## 编译&安装

### 1. 获得`鼠鬚管`源码

```bash
git clone --recursive --depth 1 git@github.com:rime/squirrel.git
```

~~2. 使用brew安装编译时依赖~~

~~brew install cmake boost~~

### 2. 预处理

- 从 **App Store** 安装 **XCode**
- 安装完 **XCode** 之后, 安装 **command-line-tools**

```bash
xcode-select --install
```

### 3. 构建依赖: `CMake`，`boost`

- [CMake 源码: github.com/Kitware/CMake](https://github.com/Kitware/CMake)
- [CMake 主页: cmake.org](https://cmake.org)
- [boost 主页: boost.org](http://www.boost.org)

#### CMake 编译安装

```bash
git clone --recursive --depth 1 git@github.com:Kitware/CMake.git CMake
cd CMake && mkdir build && cd build && \
    ../bootstrap --parallel=12 --prefix=${CMAKE_HOME} && \
    make -j4 && \
    make install

# 验证
cmake --version

# 输出: 
cmake version 3.24.0

CMake suite maintained and supported by Kitware (kitware.com/cmake).
```

### OpenCC依赖 `Python 2`

```bash
./configure --prefix=${HOME}/local/python2 --enable-optimizations && \
    make && make install
```

#### boost构建: 更新至`1.81.0`版本

```bash
brew install icu4c
# export BUILD_UNIVERSAL=1
# 引入环境变量 
export BOOST_ROOT=/Volumes/sm/servers/boost
./bootstrap.sh --prefix=${BOOST_ROOT} --with-libraries=all --with-icu=$BREW_OPT/icu4c && \
    ./b2 headers && \
    ./b2 -j12 --prefix=${BOOST_ROOT} install # 默认安装在/usr/local目录下 
```

### 3. 构建`Squirrel`依赖

```bash
make deps && make -j12 ARCHS='x86_64' # do not use make -jX when make deps 
```

### 4. 构建成功,安装`Squirrel`

```bash
sudo make ARCHS='x86_64' install 
```

### 5. 東風破(plum)

`Rime`引入了配置工具[**東風破/plum**](https://github.com/rime/plum), 可以方便下载默认配置

```bash
git clone --depth 1 git@github.com:rime/plum.git
cd plum && \
./rime-install :preset && \
./rime-install --select :all lotem/rime-forge/lotem-packages.conf
```

修改 `installation.yml`、`squirrel.yml`、`default.yml`

### 6. 自用配置

[自用 *Squirrel* 配置 rime_custom](https://github.com/yakirChen/macOS-libs/tree/master/rime/rime_custom)

### 7. 快捷键

1. `CTRL-.`可以切换**全\半角**标点符号  
2. `CTRL-SHIFT-<数字>` 可切换输入法(简体、繁体)  
3. `SHIFT-Fn-DELETE` 从词库中删除错误的词, 然后使用方向键选中词  

#### change-log

- **2016/11/20--2017/1/3. 试用感觉良好.**
- **更新2017/1/26. 更新一版重新编译安装,稳定了许多.**
- **更新2017/3/8. 扩充词库**
- **更新2017/5/5. Upgrade boost 1.64.0**
- **更新2017/6/5. 更新预览图**
- **更新2017/9/25. 更新boost 1.65.1** _更新前清理原有版本_
- **更新2018/4/17. 引入`東風破`配置工具, 更新boost 1.67**
- **更新2018/8/25. cmake & boost 並行化加速編譯, 更新boost 1.68**
- **更新2018/9/20. macOS 10.14**
- **更新2019/6/17. Squirrel 0.12.0**
- **更新2019/11/06. macOS 10.15 Squirrel 0.14.0** **Boost 1.71**
- **更新2019/12/18. macOS 10.15.2 Squirrel 0.14.0 Boost 1.72.0 CMake 3.16.1**
- **更新2020/05/13. macOS 10.15.4**
- **更新2021/02/01. macOS 11.1 Boost 1.75.0 CMake 3.19.4 ARCHS='x86_64'**
- **更新2021/06/07. macOS 11.4 Boost 1.76.0 CMake 3.20.3 ARCHS='x86_64'**
- **更新2021/09/23. macOS 11.6 Boost 1.77.0 CMake 3.21.3 ARCHS='x86_64'**
- **更新2021/10/20. macOS 12.0.1 ARCHS='x86_64'**
- **更新2022/08/16. macOS 12.5 Boost 1.80.0 CMake 3.24.0 ARCHS='x86_64' HEAD[fb7e36d]**

---

参考:

[xcode 11 code sign error](https://blog.csdn.net/u012186949/article/details/38756949)

[「鼠须管」的调教笔记](https://scomper.me/gtd/-shu-xu-guan-de-diao-jiao-bi-ji) 膜拜大神

[Cering Linux编译和安装boost库](http://cering.github.io/2015/10/30/Linux编译和安装boost库/)

[Squirrel 官方安装参考](https://github.com/rime/squirrel/blob/master/INSTALL.md)  
