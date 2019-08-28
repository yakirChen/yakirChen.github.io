---
layout: post
title: Install PostgreSQL From A Tarball
date: 2018/11/23
tags: [PostgreSQL,macOS,Data]
categories: [Tech]
toc: true
description: Install PostgreSQL From A Tarball
---

## 安装包下载

[PostgreSQL macOS packages](https://www.postgresql.org/download/macosx/)
[enterprisedb postgresql binaries](https://www.enterprisedb.com/download-postgresql-binaries)

## 初始化
```bash
export PGSOCKET=/Volumes/Toshiba/repos/postgresql
export PGDATA=/Volumes/Toshiba/repos/postgresql/data
export PGWAL=/Volumes/Toshiba/repos/postgresql/wal
initdb -D ${PGDATA} -W -X ${PGWAL} -E UTF8 -A scram-sha-256
# 初始化的过程中不要在MySQL的`data`目录中放任何内容
```

~~## 使用SSL链接~~
~~```bash~~
~~openssl req -new -x509 -days 36500 -nodes -text -out ${PGDATA}/server.crt -keyout ${PGDATA}/server.key~~
~~chmod og-rwx ${PGDATA}/server.key~~
~~```~~

## 启动服务
```bash
pg_ctl -D ${PGDATA} start
```

## 命令行客户端连接
```bash
./psql -U yakir -h ${PGSOCKET} -d postgres
```

## 创建schema
```bash
CREATE DATABASE yakirdb 
WITH OWNER = yakir 
   ENCODING = 'UTF8' 
   TABLESPACE = pg_default 
   LC_COLLATE = 'zh_CN.UTF-8' 
   LC_CTYPE = 'zh_CN.UTF-8' 
   CONNECTION LIMIT = -1 
   TEMPLATE template0;
```
