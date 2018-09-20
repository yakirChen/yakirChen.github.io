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

![鼠须管黑色主题](/imgs/squirrel-screenshot/rime_black.png)

2). **白色主题**

![鼠须管白色主题](/imgs/squirrel-screenshot/rime_white.png)


## 编译&安装

### 1. 获得`鼠鬚管`源码:

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

+ [CMake 源码: github.com/Kitware/CMake](https://github.com/Kitware/CMake)
+ [CMake 主页: cmake.org](https://cmake.org)
+ [boost 主页: boost.org](http://www.boost.org)

#### CMake 编译安装

```bash
git clone --recursive --depth 1 git@github.com:Kitware/CMake.git CMake
cd CMake && mkdir build && cd build && \
    ../bootstrap --parallel=4 --prefix=${CMAKE_HOME} && \
    make -j4 && \
    make install

# 验证
cmake --version

# 输出: 
# cmake version 3.12.20180920-g2d119e
# CMake suite maintained and supported by Kitware (kitware.com/cmake).
```

#### boost构建: 更新至`1.68`版本

```bash
./bootstrap.sh --prefix=${LOCAL} --with-libraries=all && \
    ./b2 -j4 && \
    ./b2 -j4 --prefix=${LOCAL} install # 默认安装在/usr/local目录下 

# 引入环境变量 
export BOOST_ROOT=${LOCAL}
```

### 3. 构建`Squirrel`依赖

```bash
make deps && make -j4 # do not use make -jX when make deps 
```

### 4. 构建成功,安装`Squirrel`

```bash
sudo make install
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

###### change-log

+ **从2016/11/20. 试用到2017/1/3. 感觉良好.**
+ **更新2017/1/26. 更新一版重新编译安装,稳定了许多.**
+ **更新2017/3/8. 扩充词库**
+ **更新2017/5/5. Upgrade boost 1.64.0**
+ **更新2017/6/5. 更新预览图**
+ **更新2017/9/25. 更新boost 1.65.1**(更新前记得清理原有版本)
+ **更新2018/4/17. 引入`東風破`配置工具, 更新boost 1.67**
+ **更新2018/8/25. cmake & boost 並行化加速編譯, 更新boost 1.68**
+ **更新2018/9/20. macOS 10.14**

---

参考:

[Cering Linux编译和安装boost库](http://cering.github.io/2015/10/30/Linux编译和安装boost库/)  
[Squirrel 官方安装参考](https://github.com/rime/squirrel/blob/master/INSTALL.md)  
