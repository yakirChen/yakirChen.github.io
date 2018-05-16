---
title: Rust Develop(Learn) Environment (macOS)
date: 2018/04/08
tags: [Rust, Cargo, Racer]
categories: [Tech]
toc: true
description: Rust Develop(Learn) Environment (macOS)
---

## 两种安装方式
1. 传统方式安装
2. `rustup`方式安装

## Ready
- 安装 `Rust` 的方式使用了解包归档压缩包, [点击下载: rust 1.25.0](https://static.rust-lang.org/dist/rust-1.25.0-x86_64-apple-darwin.tar.gz)
- `Rust` 源码包 [rustc-1.25.0-src.tar.gz](https://static.rust-lang.org/dist/rustc-1.25.0-src.tar.gz) _配置编辑器时用_
- 代码提示工具 [`racer-rust/racer`](https://github.com/racer-rust/racer)
- **rustup**工具安装脚本 [`rustup.sh`](https://rustup.rs)
- **rustup**工具 [`rustup.rs`](https://github.com/rust-lang-nursery/rustup.rs)

## 配置

### 预先设置环境变量
```bash
export RUST_HOME=${LOCAL}/rust
export RUSTUP_HOME=${VOLUMES_SD}/repos/rustup
export RUST_SRC_PATH=${RUSTUP_HOME}/toolchains/nightly-x86_64-apple-darwin/lib/rustlib/src/rust/src
export CARGO_HOME=${VOLUMES_SD}/repos/cargo # cargo 本地包仓库目录
export PATH=$RUST_HOME/bin:$CARGO_HOME/bin:$PATH
```

### 传统方式安装Rust
```bash
# 解压Rust安装
tar zxvf rust-xxx-tar.gz -C ~/servers/rust
# 解压Rust源码包 提取其中的src文件放到 `~/servers/rust` 目录下
tar zxvf rustc-xxx-src.tar.gz
# 我这本地把源码目录放在了其他目录中, 建立了软链
ln -s /Volumes/sd/servers/rust_src/src ${RUST_HOME}/src 
```

### **Rustup**方式安装Rust

#### 安装**rustup**
```bash
curl https://sh.rustup.rs -sSf | sh -s -- \
    --no-modify-path \
    --default-toolchain nightly \
    -y
```
[输出提示信息](https://gist.github.com/yakirChen/b36bed33ec1a5e34b27c7144511cbb5a)
`Cargo`的安装目录在预先设置的环境变量中已经配置完成  
添加`oh-my-zsh`插件  
```bash
rustup completions zsh > /Users/yakir/.oh-my-zsh/custom/plugins/rustup/_rustup
# 在 ~/.zshrc 的plugins列表中添加 `rustup`
```

#### rustup 基础使用

[:see_no_evil: 详见官方](https://github.com/rust-lang-nursery/rustup.rs)

- 安装不同通道的`Rust`
```bash
rustup install nightly
rustup install beta
rustup install stable
```

- 更新rust和rutup
```bash
rustup update && \
    rustup self update
```

- 指定运行特定的通道(版本)
```bash
rustup run stable cargo --version
rustup run nightly rustc --version

cargo +beta --version
cargo +nightly --version
```

#### 通过rustup安装组件
```bash
rustup component add rust-src --toolchain nightly
rustup component add rls-preview --toolchain nightly
rustup component add rust-analysis --toolchain nightly
```

#### alias
为了偷懒 :alien:
```bash
alias rnr="rustup run nightly"
alias rsr="rustup run stable"
alias rbr="rustup run beta"
alias rsu="rustup self update"
alias ru="rustup update" 
```

#### 测试 Rust 可用
```rust
// hello.rs
fn main() {
	println!("hello");
}
```

编译 & 运行 
```bash
rustc hello.rs
./hello 
# hello
```

#### 测试 Cargo 可用
```bash
cargo init --bin honey
#      Created binary (application) project
cd honey
cargo run
#   Compiling honey v0.1.0 (file:///Users/yakir/Think/honey)
#    Finished dev [unoptimized + debuginfo] target(s) in 1.18 secs
#     Running `target/debug/honey`
#Hello, world!
```

#### Racer 安装
- 安装方式一
```bash
cargo install racer
```

- 安装方式二(源码安装)
```bash
git clone --depth 1 git@github.com:racer-rust/racer.git

# release 编译包
cd racer; cargo build --release # 编译之后二进制包在 ./target/release/racer

# debug 编译包
cd racer; cargo build # 编译之后二进制包在 ./target/debug/racer

# 复制二进制包到 环境变量中的路径中 我的 ~/Developer/local/bin 目录添加在 PATH 中
```

#### 测试 Racer 可用
```bash
racer complete std::io::B

# 输出类似信息
# MATCH BufRead,1363,10,/Users/yakir/Developer/server/rust/src/libstd/io/mod.rs,Trait,pub trait BufRead: Read
# MATCH Bytes,1984,11,/Users/yakir/Developer/server/rust/src/libstd/io/mod.rs,Struct,pub struct Bytes<R>
# MATCH BufReader,50,11,/Users/yakir/Developer/server/rust/src/libstd/io/buffered.rs,Struct,pub struct BufReader<R>
# MATCH BufWriter,378,11,/Users/yakir/Developer/server/rust/src/libstd/io/buffered.rs,Struct,pub struct BufWriter<W: Write>
```

#### 其他扩展安装
```bash
rustup run stable cargo install mdbook cargo-src
```

### 编辑器支持

#### Sublime Text 整合

Sublime Text 3 [插件](https://packagecontrol.io/installation)安装, `ctrl + ~`启动控制台输入  
```bash
import urllib.request,os,hashlib; h = '6f4c264a24d933ce70df5dedcf1dcaee' + 'ebe013ee18cced0ef93d5f746d80ef60'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```

`command + shift + p` 输入 **Install Package** 安装  
1. 组合一
  - [RustAutoComplete](https://github.com/defuz/RustAutoComplete)
  - [anaconda_rust](https://github.com/DamnWidget/anaconda_rust)

2. 组合二
  - [Rust Enhanced](https://github.com/rust-lang/rust-enhanced)


---

Reference:  
[rustup.rs: https://github.com/rust-lang-nursery/rustup.rs](https://github.com/rust-lang-nursery/rustup.rs)

