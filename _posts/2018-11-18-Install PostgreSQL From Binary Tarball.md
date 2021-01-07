---
layout: post
title: Install PostgreSQL From Binary Tarball
date: 2018/11/23
tags: [PostgreSQL,macOS,Data]
categories: [Tech]
toc: true
description: Install PostgreSQL From Binary Tarball
---

## 安装包下载 (当前版本13)

[PostgreSQL macOS packages](https://www.postgresql.org/download/macosx/)
[enterprisedb postgresql binaries](https://www.enterprisedb.com/download-postgresql-binaries)

## 初始化
```bash
export PGSOCKET=/Volumes/sm/repos/pgsql
export PGDATA=/Volumes/sm/repos/pgsql/data
export PGWAL=/Volumes/sm/repos/pgsql/wal
export PGLOG=/Volumes/sm/repos/pgsql/pglog.log
export PGCTLLOG=/Volumes/sm/repos/pgsql/pgctllog.log
./initdb -D ${PGDATA} -W -X ${PGWAL} -E UTF8
./initdb -D ${PGDATA} -W -X ${PGWAL} -E Unicode
./initdb -D ${PGDATA} -W -X ${PGWAL} -E Unicode --no-locale
# initdb -D ${PGDATA} -W -X ${PGWAL} -E UTF8 -A scram-sha-256
# 初始化的过程中不要在MySQL的`data`目录中放任何内容
# 支持的字符编码:
# https://www.postgresql.org/docs/13/multibyte.html#MULTIBYTE-CHARSET-SUPPORTED
```

~~## 使用SSL链接~~
~~```bash~~
~~openssl req -new -x509 -days 36500 -nodes -text -out ${PGDATA}/server.crt -keyout ${PGDATA}/server.key~~
~~chmod og-rwx ${PGDATA}/server.key~~
~~```~~

## 启动服务
```bash
./pg_ctl -D ${PGDATA} -l ${PGLOG} start
```

## 关闭服务
```bash
./pg_ctl -D ${PGDATA} -l ${PGLOG} stop
```

## 命令行客户端连接
```bash
./psql -U yakir -L ${PGCTLLOG} -d postgres
# ./psql -U yakir -L ${PGCTLLOG} -h ${PGSOCKET} -d postgres
```

## pgAdmin
日志: `~/.pgadmin/pgadmin4.log`
配置文件; `pgAdmin.app/web/config_local.py`
```properties
import logging

# Application log level - one of:
#   CRITICAL 50
#   ERROR    40
#   WARNING  30
#   SQL      25
#   INFO     20
#   DEBUG    10
#   NOTSET    0
CONSOLE_LOG_LEVEL = logging.DEBUG
FILE_LOG_LEVEL = logging.DEBUG

# Log format.
CONSOLE_LOG_FORMAT = '%(asctime)s: %(levelname)s\t%(name)s:\t%(message)s'
FILE_LOG_FORMAT = '%(asctime)s: %(levelname)s\t%(name)s:\t%(message)s'

# Log file name
LOG_FILE = '/desired/path/to/pgadmin4.log'
```

## 创建database
```bash
CREATE DATABASE book_postgre WITH OWNER = yakir 
   ENCODING = 'UTF8' 
   TABLESPACE = pg_default 
   LC_COLLATE = 'zh_CN.UTF-8' 
   LC_CTYPE = 'zh_CN.UTF-8' 
   CONNECTION LIMIT = 6
   TEMPLATE template0;
```
