---
title: Turn on performance mode for macOS
date: 2018/11/23
tags: [PostgreSQL,macOS,Data]
categories: [Tech]
toc: true
description: macOS 开启性能模式
---


## 引用
+ [Turn on performance mode for macOS Server](https://support.apple.com/en-us/HT202528)
+ [针对 macOS Server 开启性能模式](https://support.apple.com/zh-cn/HT202528)


## 重置NVRAM

Option + Command + P + R

> 将 Mac 关机，然后开机并立即同时按住以下四个按键：Option、Command、P 和 R。您可以在大约 20 秒后松开这些按键，在此期间您的 Mac 可能看似在重新启动。
>  
> 在会发出启动声的 Mac 电脑上，您可以在两次启动声之后松开这些按键。
>  
> 在配有 Apple T2 安全芯片的 Mac 电脑上，您可以在 Apple 标志出现并再次消失后松开这些按键。

```shell
nvram boot-args
```
boot-args NVRAM 变量。如果您看到 serverperfmode=1，则说明性能模式已开启


开启高性能模式
```shell
sudo nvram boot-args="serverperfmode=1 $(nvram boot-args 2>/dev/null | cut -f 2-)"
```

关闭高性能模式
```shell
sudo nvram boot-args="$(nvram boot-args 2>/dev/null | sed -e $'s/boot-args\t//;s/serverperfmode=1//')"
```

`sysctl -a`查看的大致不同
```properties
kern.maxvnodes: 263168
kern.maxproc: 4256
kern.maxfilesperproc: 49152
kern.maxprocperuid: 2837
kern.ipc.somaxconn: 128
kern.ipc.nmbclusters: 65536
kern.ipc.njcl: 21840
kern.bootargs: 
kern.timer.longterm.threshold: 1000
kern.timer.longterm.qlen: 42
kern.timer.longterm.scan_pauses: 1
kern.ipc.sbmb_cnt_peak: 1078
net.inet.ip.maxfragpackets: 2048
net.inet.ip.maxchainsent: 14
net.inet.tcp.fastopen_backlog: 10
net.inet.tcp.tcbhashsize: 4096
net.inet6.ip6.maxfragpackets: 2048
net.inet6.ip6.maxfrags: 4096
```

```properties
kern.maxvnodes: 600000
kern.maxproc: 10000
kern.maxfilesperproc: 300000
kern.maxprocperuid: 7500
kern.ipc.somaxconn: 2048
kern.ipc.nmbclusters: 131072
kern.ipc.njcl: 43688
kern.bootargs: serverperfmode=1 
kern.timer.longterm.threshold: 0
kern.timer.longterm.qlen: 0
kern.timer.longterm.scan_pauses: 0
kern.ipc.sbmb_cnt_peak: 1117
net.inet.ip.maxfragpackets: 4096
net.inet.ip.maxchainsent: 4
net.inet.tcp.fastopen_backlog: 400
net.inet.tcp.tcbhashsize: 16384
net.inet6.ip6.maxfragpackets: 4096
net.inet6.ip6.maxfrags: 8192
```

