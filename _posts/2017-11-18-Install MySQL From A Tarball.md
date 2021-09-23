---
title: Install MySQL From A Tarball
date: 2017/11/18
tags: [MySQL,macOS,Data]
categories: [Tech]
toc: true
description: Install MySQL From A Tarball
---

## macOS

### 安装前环境变量配置

```bash
export MYSQL_BASE_DIR=/Volumes/sm/servers/mysql
export MYSQL_COMMON_DIR=/Volumes/sm/repos/mysql
export MYSQL_DATA_DIR=/Volumes/sm/repos/mysql/data
export MYSQL_LOGS_DIR=/Volumes/sm/repos/mysql/logs
# 初始化的过程中不要在MySQL的`data`目录中放任何内容
```

### 初始化

```bash
mkdir -p ${MYSQL_LOGS_DIR}
mkdir /Volumes/sm/repos/mysql/tmpdir
mysqld --initialize \
	--user=yakir \
	--basedir=${MYSQL_BASE_DIR} \
	--datadir=${MYSQL_DATA_DIR} \
	--log-error=${MYSQL_LOGS_DIR}/error.log \
	--pid-file=${MYSQL_COMMON_DIR}/mysql.pid \
	--socket=${MYSQL_COMMON_DIR}/mysql.sock --port=3306
```

### 获取初始登录密码

```bash
cat ${MYSQL_LOGS_DIR}/error.log
```

### 启动服务

```bash
mysqld --user=yakir \
	--basedir=${MYSQL_BASE_DIR} \
	--datadir=${MYSQL_DATA_DIR} \
	--log-error=${MYSQL_LOGS_DIR}/error.log \
	--pid-file=${MYSQL_COMMON_DIR}/mysql.pid \
	--socket=${MYSQL_COMMON_DIR}/mysql.sock \
	--server-id=80000 \
	--log-bin=mysql-log \
	--log-bin-index=mysql-log \
	--binlog-format=ROW \
	--binlog-row-event-max-size=1024 \
	--port=3306 &
```

### 安全配置向导(optional)

```shell
./mysql_secure_installation --socket=${MYSQL_COMMON_DIR}/mysql.sock
```

### 登录MySQL进行用户初始化

```bash
mysql -uroot -p --socket=${MYSQL_COMMON_DIR}/mysql.sock
# 输入之前从日志中获取的初始密码

# 修改root默认密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'qweasd';

# 创建用户
CREATE USER yakir IDENTIFIED WITH mysql_native_password BY 'qweasd';
# ALTER USER yakir IDENTIFIED WITH mysql_native_password BY 'qweasd';

# 删除用户
# DROP USER 'canal'@'%';

# 赋权
GRANT ALL PRIVILEGES ON *.* TO 'yakir'@'%' ;
FLUSH PRIVILEGES;
```

### 关闭MySQL服务

```bash
mysqladmin shutdown --socket=${MYSQL_COMMON_DIR}/mysql.sock -uroot -p
```

## Debian

_以下操作都是使用系统root用户_

### 安装前环境变量配置

```bash
# 目录变量定义
MYSQL_BASE_DIR='/data/mysql'
MYSQL_DATA_DIR='/data/store/mysql'
MYSQL_LOGS_DIR='/data/store/logs/mysql'
```

### 依赖包check

```bash
apt-cache search libaio1
apt-get install libaio1 
```

### 系统配置

```bash
# 添加用户组
groupadd mysql 
# 添加mysql用户
useradd -r -g mysql -s /bin/false mysql 

# 修改目录权限
mkdir -p ${MYSQL_DATA_DIR}
chmod 750 ${MYSQL_DATA_DIR}
chown -R mysql .
chgrp -R mysql .
chown -R mysql ${MYSQL_DATA_DIR}
chgrp -R mysql ${MYSQL_DATA_DIR}
```

### 初始化

```bash
/data/mysql/bin/mysqld --initialize \
        --user=mysql \
        --basedir=${MYSQL_BASE_DIR} \
        --datadir=${MYSQL_DATA_DIR} \
        --log-error=${MYSQL_LOGS_DIR}/error.log \
        --pid-file=${MYSQL_COMMON_DIR}/mysql.pid \
        --socket=${MYSQL_COMMON_DIR}/mysql.sock \
        --port=3306

/data/mysql/bin/mysql_ssl_rsa_setup \
        --user=mysql \
        --basedir=${MYSQL_BASE_DIR} \
        --datadir=${MYSQL_DATA_DIR} \
        --log-error=${MYSQL_LOGS_DIR}/error.log \
        --pid-file=${MYSQL_COMMON_DIR}/mysql.pid \
        --socket=${MYSQL_COMMON_DIR}/mysql.sock \
        --port=3306
```

### 获取mysql初始化密码 & 更改存储目录权限

```bash
cat ${MYSQL_LOGS_DIR}/error.log

chown -R root .
chown -R mysql /data/store/mysql
```

### 启动实例

```bash
echo '启动mysql实例'
/data/mysql/bin/mysqld \
        --user=mysql \
        --basedir=${MYSQL_BASE_DIR} \
        --datadir=${MYSQL_DATA_DIR} \
        --log-error=${MYSQL_LOGS_DIR}/error.log \
        --pid-file=${MYSQL_COMMON_DIR}/mysql.pid \
        --socket=${MYSQL_COMMON_DIR}/mysql.sock \
        --server-id=90000 \
        --log-bin=mysql-log \
        --log-bin-index=mysql-log \
        --binlog-format=ROW \
        --binlog-row-event-max-size=1024 \
        --port=3306 &

# 启动实例示例
# /usr/local/mysql/bin/mysqld \
        --basedir=/usr/local/mysql \
        --datadir=/opt/mysql/data \
        --plugin-dir=/usr/local/mysql/lib/plugin \
        --user=mysql \
        --log-error=/opt/mysql/data/error.log \
        --pid-file=/opt/mysql/data/localhost.localdomain.pid \
        --socket=/opt/mysql/data/mysql.sock \
        --port=3306

# 停止实例
# /data/mysql/bin/mysqladmin shutdown --socket=/data/store/mysql/mysql.sock -uroot -p
```

### 添加用户 & 用户修改

*见macOS安装*

#### update 
- 2018/10/23 mysql 8.0.13 macOS 10.14
- 2019/08/13 mysql 8.0.17 macOS 10.14

--- 

Reference: 

[MySQL安装包下载](https://dev.mysql.com/downloads/mysql/)  
[MySQL用户文档 5.7](https://dev.mysql.com/doc/refman/5.7/en/)  
[MySQL用户文档 8.0](https://dev.mysql.com/doc/refman/8.0/en/)  
