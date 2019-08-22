---
layout: post
title: Rust Develop(Learn) Environment (macOS/windows)
date: 2018/04/08
tags: [Rust, Cargo, Racer]
categories: [Tech]
toc: true
description: Rust Develop(Learn) Environment (macOS)
---

## 两种安装方式
~~传统方式安装~~
`rustup`方式安装(多版本管理 stable / beta / nightly)

## Ready
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

### 安装**rustup**
```bash
curl https://sh.rustup.rs -sSf | sh -s -- \
    --no-modify-path \
    --default-toolchain nightly \
    -y
    
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- \
    --no-modify-path \
    --default-toolchain nightly \
    -y
```
```powershell
rustup-init.exe --no-modify-path --default-toolchain nightly  -y
```

[输出提示信息](https://gist.github.com/yakirChen/b36bed33ec1a5e34b27c7144511cbb5a)
`Cargo`的安装目录在预先设置的环境变量中已经配置完成  
添加`oh-my-zsh`插件  
```bash
rustup completions zsh > /Users/yakir/.oh-my-zsh/custom/plugins/rustup/_rustup
# 在 ~/.zshrc 的plugins列表中添加 `rustup`
```

### rustup 基础使用

[🙈 详见官方](https://github.com/rust-lang-nursery/rustup.rs)

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

### 通过rustup安装组件
```bash
# nightly
rustup component add rust-src --toolchain nightly
rustup component add rls --toolchain nightly
rustup component add rust-analysis --toolchain nightly
rustup component add rustfmt --toolchain nightly
rustup component add clippy --toolchain nightly
rustup component add miri --toolchain nightly

#beta
rustup component add rust-src --toolchain beta
rustup component add rls --toolchain beta
rustup component add rust-analysis --toolchain beta
rustup component add rustfmt --toolchain beta
rustup component add clippy --toolchain beta
rustup component add miri --toolchain beta

# stable
rustup component add rust-src --toolchain stable
rustup component add rls --toolchain stable
rustup component add rust-analysis --toolchain stable
rustup component add rustfmt --toolchain stable
rustup component add clippy --toolchain stable
rustup component add miri --toolchain stable
```

```bash
# 运行clippy
cargo run --bin cargo-clippy --manifest-path=path_to_clippys_Cargo.toml
```

#### alias
为了偷懒👽
```bash
alias rnr="rustup run nightly"
alias rsr="rustup run stable"
alias rbr="rustup run beta"
alias rsu="rustup self update"
alias ru="rustup update" 
```

### 测试 Rust 可用
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

### 测试 Cargo 可用
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

### 组件安装
- 安装方式一

```bash
cargo install -f -j 6 --all-features -v racer mdbook cargo-src fd-find
strip xxx....
```

- 安装方式二(源码安装)

```bash
git clone --depth 1 git@github.com:racer-rust/racer.git

cd racer && cargo build --release # 编译之后二进制包在 ./target/release/racer

```

#### 测试 Racer 可用
```bash
racer complete std::io::B

# 输出类似信息
MATCH BufRead,1551,10,/Users/yakir/local/rust/toolchains/nightly-x86_64-apple-darwin/lib/rustlib/src/rust/src/libstd/io/mod.rs,Trait,pub trait BufRead: Read
MATCH Bytes,2171,11,/Users/yakir/local/rust/toolchains/nightly-x86_64-apple-darwin/lib/rustlib/src/rust/src/libstd/io/mod.rs,Struct,pub struct Bytes<R>
MATCH BufReader,51,11,/Users/yakir/local/rust/toolchains/nightly-x86_64-apple-darwin/lib/rustlib/src/rust/src/libstd/io/buffered.rs,Struct,pub struct BufReader<R>
MATCH BufWriter,412,11,/Users/yakir/local/rust/toolchains/nightly-x86_64-apple-darwin/lib/rustlib/src/rust/src/libstd/io/buffered.rs,Struct,pub struct BufWriter<W: Write>
```

### 编辑器支持


#### vim 整合


#### Sublime Text 整合

Sublime Text 3 [Package Control](https://packagecontrol.io/installation)安装, `ctrl + ~`启动控制台输入  
```bash
import urllib.request,os,hashlib; h = '6f4c264a24d933ce70df5dedcf1dcaee' + 'ebe013ee18cced0ef93d5f746d80ef60'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```

1. 使用 **Package Control** 安装 [Rust Enhanced](https://github.com/rust-lang/rust-enhanced)
` + Shift + P` 输入 **Install Package** 安装  

2. [Language Server Protocol](https://github.com/tomv564/LSP)
clone 代码到 sublime Packages 目录中 `${HOME}/Library/Application Support/Sublime Text 3/Packages`
` + Shift + P` 输入 **Satisfy Dependencies** 安装  
_控制台输出 "Package Control: All dependencies have been satisfied"_
LSP文档 [LSP.readthedocs.io](https://lsp.readthedocs.io/), [docs](https://github.com/tomv564/LSP/blob/master/docs/index.md)


~~1. 组合一~~
  ~~- [RustAutoComplete](https://github.com/defuz/RustAutoComplete)~~
  ~~- [anaconda_rust](https://github.com/DamnWidget/anaconda_rust)~~

~~2. 组合二~~


### 工具集
+ [lsd](https://github.com/Peltoche/lsd)
+ [hyperfine](https://github.com/sharkdp/hyperfine)
+ [fd](https://github.com/sharkdp/fd)
+ [cargo-watch](https://github.com/passcod/cargo-watch)

---

Reference:  
[rustup.rs: https://github.com/rust-lang-nursery/rustup.rs](https://github.com/rust-lang-nursery/rustup.rs)