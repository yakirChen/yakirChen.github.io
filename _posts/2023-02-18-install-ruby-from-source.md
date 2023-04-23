---

title: Install Ruby From Source
date: 2023/02/18
tags: [Ruby]

---

## 前置

```shell
brew install libyaml openssl readline
```

## 编译

```shell
./configure --prefix=/Users/yakir/local/ruby \
    --disable-silent-rules \
    --without-gmp \
    --with-opt-dir=${BREW_OPT}/openssl:${BREW_OPT}/libyaml:${BREW_OPT}/readline && \
    make -j12 && make install
```


## 更新gem

```shell
gem update --system
```

### 安装 rubygems

```shell
gem install cocoapods
gem install github-pages
```