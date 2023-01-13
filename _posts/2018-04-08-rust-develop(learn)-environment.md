---
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
```shell
export RUST_HOME=${LOCAL}/rust
export RUSTUP_HOME=${VOLUMES_SD}/repos/rustup
export RUST_SRC_PATH=${RUSTUP_HOME}/toolchains/nightly-x86_64-apple-darwin/lib/rustlib/src/rust/src
export CARGO_HOME=${VOLUMES_SD}/repos/cargo # cargo 本地包仓库目录
export PATH=$RUST_HOME/bin:$CARGO_HOME/bin:$PATH
```

### 安装**rustup**
```shell
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

```shell
rustup completions zsh > /Users/yakir/.ohmyzsh/custom/plugins/rustup/_rustup
# 在 ~/.zshrc 的plugins列表中添加 `rustup`
```

### rustup 基础使用

[🙈 详见官方](https://github.com/rust-lang-nursery/rustup.rs)

- 安装不同通道的`Rust`
```shell
rustup install nightly
rustup install beta
rustup install stable
```

- 更新rust和rutup
```shell
rustup update && \
    rustup self update
```

- 指定运行特定的通道(版本)

```shell
rustup run stable cargo --version
rustup run nightly rustc --version

cargo +beta --version
cargo +nightly --version
```

### 通过rustup安装组件

```shell
# nightly
rustup component add rust-src --toolchain nightly
rustup component add rls --toolchain nightly
rustup component add rust-analysis --toolchain nightly
rustup component add rustfmt --toolchain nightly
rustup component add clippy --toolchain nightly
rustup component add miri --toolchain nightly
rustup +nightly component add miri

#beta
rustup component add rust-src --toolchain beta
rustup component add rls --toolchain beta
rustup component add rust-analysis --toolchain beta
rustup component add rustfmt --toolchain beta
rustup component add clippy --toolchain beta
rustup component add miri --toolchain beta
rustup +beta component add miri

# stable
rustup component add rust-src --toolchain stable
rustup component add rls --toolchain stable
rustup component add rust-analysis --toolchain stable
rustup component add rustfmt --toolchain stable
rustup component add clippy --toolchain stable
rustup component add miri --toolchain stable
rustup +stable component add miri
```

```shell
# 运行clippy
cargo run --bin cargo-clippy --manifest-path=path_to_clippys_Cargo.toml
```

#### alias
为了偷懒👽
```shell
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
```shell
rustc hello.rs
./hello 
# hello
```

### 测试 Cargo 可用
```shell
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

```shell
cargo install -f -j 6 --all-features -v racer mdbook cargo-src fd-find
strip xxx....
```

- 安装方式二(源码安装)

```shell
git clone --depth 1 git@github.com:racer-rust/racer.git

cd racer && cargo build --release # 编译之后二进制包在 ./target/release/racer

```

#### 测试 Racer 可用
```shell
racer complete std::io::B

# 输出类似信息
MATCH BufRead,1943,10,/Users/yakir/local/rust/toolchains/nightly-x86_64-apple-darwin/lib/rustlib/src/rust/library/std/src/io/mod.rs,Trait,pub trait BufRead: Read
MATCH Bytes,2613,11,/Users/yakir/local/rust/toolchains/nightly-x86_64-apple-darwin/lib/rustlib/src/rust/library/std/src/io/mod.rs,Struct,pub struct Bytes<R>
MATCH BufReader,48,11,/Users/yakir/local/rust/toolchains/nightly-x86_64-apple-darwin/lib/rustlib/src/rust/library/std/src/io/buffered/bufreader.rs,Struct,pub struct BufReader<R>
MATCH BufWriter,70,11,/Users/yakir/local/rust/toolchains/nightly-x86_64-apple-darwin/lib/rustlib/src/rust/library/std/src/io/buffered/bufwriter.rs,Struct,pub struct BufWriter<W: Write>
```

### 编辑器支持


#### vim 整合


#### Sublime Text 整合

Sublime Text 3 [Package Control](https://packagecontrol.io/installation)安装, `ctrl + ~`启动控制台输入  
```shell
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
+ amxml
+ bat
+ cargo-deps
+ cargo-lipo
+ cargo-modules
+ [cargo-watch](https://github.com/passcod/cargo-watch)
+ cargo-web
+ cbindgen
+ evcxr
+ evcxr_repl
+ fblog
+ [fd-find](https://github.com/sharkdp/fd)
+ ferris-says
+ grapple
+ hexyl
+ [hyperfine](https://github.com/sharkdp/hyperfine)
+ jql
+ jsonxf
+ [lsd](https://github.com/Peltoche/lsd)
+ mdbook
+ mdcat
+ qrencode
+ racer
+ [rget](https://github.com/Arcterus/rget.git?branch=refactor#f86d1b0f)
+ ripgrep
+ [shadowsocks-rust](https://github.com/shadowsocks/shadowsocks-rust.git#6f4f7546)
+ [silicon](https://github.com/Aloxaf/silicon#06671499)
+ tealdeer
+ xterm256_converter


---

Reference:  
[rustup.rs: https://github.com/rust-lang-nursery/rustup.rs](https://github.com/rust-lang-nursery/rustup.rs)
