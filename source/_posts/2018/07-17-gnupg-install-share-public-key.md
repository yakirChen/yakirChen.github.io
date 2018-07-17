---
title: GnuPG Install And Share Public-Key
date: 2018/07/17
tags: [macOS,Tech]
categories: [Tech]
toc: true
description: GnuPG Install And Share Public-Key
---


使用过程中不是很顺利, 是不是因为编译安装产生的问题, 不过还是想记录一下, 作为备忘录



## Prepare

[GnuPG 源码包下载, 包括依赖](https://www.gnupg.org/download/index.html)

|源码包|版本|链接|
|:---|:---|:---|
|GnuPG| 2.2.9 |[http://www.ring.gr.jp/pub/net/gnupg/gnupg/](http://www.ring.gr.jp/pub/net/gnupg/gnupg/) |
|Libgpg-error|    1.32| [http://www.ring.gr.jp/pub/net/gnupg/libgpg-error/](http://www.ring.gr.jp/pub/net/gnupg/libgpg-error/) |
|Libgcrypt|   1.8.3| [http://www.ring.gr.jp/pub/net/gnupg/libgcrypt/](http://www.ring.gr.jp/pub/net/gnupg/libgcrypt/) |
|Libksba| 1.3.5 | [http://www.ring.gr.jp/pub/net/gnupg/libksba/](http://www.ring.gr.jp/pub/net/gnupg/libksba/) |
|Libassuan|   2.5.1| [http://www.ring.gr.jp/pub/net/gnupg/libassuan/](http://www.ring.gr.jp/pub/net/gnupg/libassuan/) |
|ntbTLS|  0.1.2| [http://www.ring.gr.jp/pub/net/gnupg/ntbtls/](http://www.ring.gr.jp/pub/net/gnupg/ntbtls/) |
|nPth|    1.6| [http://www.ring.gr.jp/pub/net/gnupg/npth/](http://www.ring.gr.jp/pub/net/gnupg/npth/) |
|Pinentry|    1.1.0| [http://www.ring.gr.jp/pub/net/gnupg/pinentry/](http://www.ring.gr.jp/pub/net/gnupg/pinentry/) |


## 构建安装 
```bash
# libgpg-error
./configure --prefix=${LOCAL} \
    --disable-dependency-tracking \
    --disable-silent-rules \
    --enable-static && \
    make -j4 && \
    make install
# libgcrypt
./configure --disable-dependency-tracking \
    --disable-silent-rules \
    --enable-static \
    --prefix=${LOCAL} \
    --disable-asm \
    --disable-jent-support && \
    make -j4 && \
    make install
# libksba
./configure --disable-dependency-tracking \
    --disable-silent-rules \
    --prefix=${LOCAL} && \
    make -j4 && \
    make install
# libassuan
./configure --disable-dependency-tracking \
    --disable-silent-rules \
    --prefix=${LOCAL} \
    --enable-static && \
    make -j4 && \
    make install
# npth
./configure --disable-dependency-tracking \
    --disable-silent-rules \
    --prefix=${LOCAL} && \
    make -j4 && \
    make install
# pinentry
./configure --disable-dependency-tracking \
    --disable-silent-rules \
    --prefix=${LOCAL}/pinentry \
    --disable-pinentry-qt \
    --disable-pinentry-qt5 \
    --disable-pinentry-gnome3 \
    --disable-pinentry-tqt \
    --disable-pinentry-fltk \
    --enable-pinentry-tty \
    --disable-pinentry-gtk2 && \
    make -j4 && \
    make install
# ntbtls
./configure --disable-dependency-tracking \
    --enable-maintainer-mode \
    --disable-silent-rules \
    --prefix=${LOCAL} \
    --disable-heartbeat-support && \
    make -j4 && \
    make install
# gnupg
./configure --disable-dependency-tracking \
      --disable-silent-rules \
      --disable-gnutls \
      --disable-ntbtls \
      --prefix=${LOCAL}/gnupg \
      --enable-symcryptrun \
      --with-pinentry-pgm=${LOCAL}/pinentry/bin/pinentry \
      --enable-all-tests \
      --disable-sqlite \
      --disable-ccid-driver && make -j4 && \
      make install
```

## 常用Command
```bash
# 生成key
gpg --full-gen-key
gpgconf --kill gpg-agent && gpg-agent --use-standard-socket --pinentry-program ${LOCAL}/pinentry/bin/pinentry --daemon
gpg --list-secret-keys --keyid-format SHORT
gpg --list-secret-keys --keyid-format LONG
gpg -vvv --debug-all --keyserver keyserver.opensuse.org --send-keys KEY-IDS

# 导出公钥
gpg --export -a KEY-ID > KEY-ID.key
```


## Share
我这边一直不能 **send key** 操作原因? 安装原因? 未探究... 

因为不能通过 `gpg --send-keys` 所以手工在页面中 `submit` 😑
用了opensuse的keyserver submit [http://keyserver.opensuse.org/](http://keyserver.opensuse.org/)

这是我提交成功的公钥:  
[http://keyserver.opensuse.org/pks/lookup?search=0x0A35CCA4&fingerprint=on&hash=on&op=vindex](http://keyserver.opensuse.org/pks/lookup?search=0x0A35CCA4&fingerprint=on&hash=on&op=vindex)  
[http://keyserver.ubuntu.com/pks/lookup?search=0x0A35CCA4&fingerprint=on&hash=on&op=get](http://keyserver.ubuntu.com/pks/lookup?search=0x0A35CCA4&fingerprint=on&hash=on&op=get)
