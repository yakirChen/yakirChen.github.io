---
layout: post
title: 想要一个独立的JS Runtime 就V8啦
date: 2018/11/09
tags: [v8,js,macOS]
categories: [Tech]
---


### 获得源码
```bash
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git

mkdir depot_tools && \
    cd depot_tools && \
    git init && \
    git remote add origin xxx.git && \
    git fetch --all && \
    git checkout master
    
export PATH=$PATH:/Volumes/Toshiba/dev/tools/depot_tools
export DEPOT_TOOLS_UPDATE=0
gclient config https://github.com/v8/v8.git
gclient sync
```



##### Reference

[depot_tools_tutorial(7) Manual Page](https://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools_tutorial.html#_setting_up)
