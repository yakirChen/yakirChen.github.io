---

title: Build Nginx on macOS
date: 2020/07/21
tags: [nginx,http2,http,h2,grpc]

---

## 安装源码包下载
- [Nginx](https://nginx.org/en/download.html)
- [LibreSSL](https://ftp.openbsd.org/pub/OpenBSD/LibreSSL/) [OpenSSL](https://www.openssl.org/source/)
- [pcre](https://ftp.pcre.org/pub/pcre/)
- [zlib](https://zlib.net/)


## 执行安装
```bash
export NGINX=/Users/yakir/local/nginx

./configure --prefix=${NGINX} \
    --sbin-path=${NGINX}/nginx \
    --conf-path=${NGINX}/nginx.conf \
    --pid-path=${NGINX}/nginx.pid \
    --with-threads \
    --with-stream \
    --with-http_v2_module \
    --with-http_ssl_module \
    --with-stream_ssl_module \
    --with-pcre-jit \
    --with-http_addition_module \
    --with-http_auth_request_module \
    --http-log-path=/Volumes/sm/logs/nginx/access.log \
    --error-log-path=/Volumes/sm/logs/nginx/error.log \
    --lock-path=/Volumes/sm/logs/nginx/nginx.lock \
    --with-http_stub_status_module \
    --with-http_realip_module \
    --with-debug \
    --with-pcre=./pcre-8.43 \
    --with-zlib=./zlib-1.2.11 \
    --with-openssl=./libressl-3.2.0 && \
    make -j 12 && make install
```

## 输出版本号
```bash
% $NGINX/nginx -v 
nginx version: nginx/1.19.1
```

## Nginx 自签名证书
- [Ref: gencert.sh](https://github.com/michaelliao/itranswarp.js/blob/master/conf/ssl/gencert.sh)
- [Ref: 自签名证书 + Nginx 实现 HTTP 升级 HTTPS](https://www.gokuweb.com/operation/d95eae05.html)



### 创建根证书
1. 生成私钥
```bash
openssl genrsa -out ca.key 4096
```

2. 生成证书请求文件
Common Name (eg, your name or your server's hostname) 是 必填项 ，要填写自己的域名或者 IP，其他都可以回车跳过
```bash
openssl req -new -key ca.key -out ca.csr
```

3. 生成字签名证书

```bash
openssl req -x509 -days 36600 -key ca.key -in ca.csr -out ca.crt
openssl x509 -req -days 36600 -in ca.csr -signkey ca.key -out ca.crt
```

4. 查看信息

查看证书请求信息(csr)
```bash
openssl req -in ca.csr -text -verify -noout
```

查看证书信息(crt)
```bash
openssl x509 -in ca.crt -text -noout
```


### 创建用户证书

1. 生成私钥
```bash
openssl genrsa -out server.key 4096
```

2. 生成证书请求
Common Name (eg, your name or your server's hostname) 是 必填项 ，要填写自己的域名或者 IP，其他都可以回车跳过
```bash
openssl req -new -key server.key -out server.csr
```

3. 生成证书_根证书对用户的证书请求签名，最终生成用户证书_
```bash
openssl ca -in server.csr -out server.crt -cert ca.crt -keyfile ca.key
openssl ca -policy policy_anything -in server.csr -out server.crt -cert ca.crt -keyfile ca.key
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -out server.crt -CAcreateserial
```

4. 查看信息

查看证书请求信息(csr)
```bash
openssl req -in server.csr -text -verify -noout
```

查看证书信息(crt)
```bash
openssl x509 -in server.crt -text -noout
```

5. 验证证书有效性
```bash
openssl verify -CAfile ca.crt server.crt
```

6. 生成 pem 格式证书（可选）
```bash
cat server.crt server.key > server.pem
```
