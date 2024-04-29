---
title: Install PostgreSQL From A Tarball
date: 2024/04/25
tags: [PostgreSQL,macOS,Data]
categories: [Tech]
toc: true
description: Install PostgreSQL From A Tarball
---

## macOS

### 安装包

(https://www.enterprisedb.com/download-postgresql-binaries)[https://www.enterprisedb.com/download-postgresql-binaries]

### 初始化

```shell
./bin/initdb -D /Volumes/sm/repos/postgre -A trust
```

### 启动

```shell
./bin/pg_ctl -D /Volumes/sm/repos/postgre -l /Volumes/sm/repos/postgre/log.log start
```


### 停止

```shell
./bin/pg_ctl -D /Volumes/sm/repos/postgre -l /Volumes/sm/repos/postgre/log.log stop
```


--- 

Reference: 

[Setup and run PostgreSQL without any administrative rights in MacOS](https://medium.com/@nofanto/setup-and-run-postgresql-without-any-administrative-rights-in-macos-69ff94f61b56)  
