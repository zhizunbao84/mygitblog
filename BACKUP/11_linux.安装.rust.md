# [linux 安装 rust](https://github.com/zhizunbao84/gitblog/issues/11)

## 准备工作
在开始安装之前，需要做一些准备。
首先确保你的 Debian 操作系统已更新到最新（应该是可选的，我当时就没做）：
`apt update && apt upgrade -y`
接下来，安装 Rust 安装过程中需要的包：
`apt install curl build-essential gcc make -y`

## 安装 Rust
Rust 由 rustup 工具安装和管理。做好准备工作之后，你现在可以使用 curl 通命令来获取 Rust 安装脚本：
`curl --proto '=https' --tlsv1.3 -sSf https://sh.rustup.rs | sh`
你会看到类似下图的提示，输入 `1` 并按回车继续。
```
info: downloading installer

Welcome to Rust!

This will download and install the official compiler for the Rust
programming language, and its package manager, Cargo.

Rustup metadata and toolchains will be installed into the Rustup
home directory, located at:

  /home/user/.rustup

This can be modified with the RUSTUP_HOME environment variable.

The Cargo home directory is located at:

  /home/user/.cargo

This can be modified with the CARGO_HOME environment variable.

The cargo, rustc, rustup and other commands will be added to
Cargo's bin directory, located at:

  /home/user/.cargo/bin

This path will then be added to your PATH environment variable by
modifying the profile files located at:

  /home/user/.profile
  /home/user/.bashrc

You can uninstall at any time with rustup self uninstall and
these changes will be reverted.

Current installation options:


   default host triple: mips64el-unknown-linux-gnuabi64
     default toolchain: stable (default)
               profile: default
  modify PATH variable: yes

1) Proceed with installation (default)
2) Customize installation
3) Cancel installation

```
整个安装需要 1 到 5 分钟，具体取决于你的网速和硬件，成功完成后，你将看到以下结果：
```
nfo: profile set to 'default'
info: default host triple is mips64el-unknown-linux-gnuabi64
info: syncing channel updates for 'stable-mips64el-unknown-linux-gnuabi64'
info: latest update on 2022-06-30, rust version 1.62.0 (a8314ef7d 2022-06-27)
info: downloading component 'cargo'
  6.0 MiB /   6.0 MiB (100 %)   2.3 MiB/s in  3s ETA:  0s
info: downloading component 'clippy'
  2.6 MiB /   2.6 MiB (100 %) 624.0 KiB/s in  2s ETA:  0s
info: downloading component 'rust-docs'
 18.3 MiB /  18.3 MiB (100 %)   4.6 MiB/s in  4s ETA:  0s
info: downloading component 'rust-std'
 22.8 MiB /  22.8 MiB (100 %)   4.7 MiB/s in  6s ETA:  0s
info: downloading component 'rustc'
 58.3 MiB /  58.3 MiB (100 %)   4.4 MiB/s in 14s ETA:  0s    
info: downloading component 'rustfmt'
  3.8 MiB /   3.8 MiB (100 %) 978.5 KiB/s in  2s ETA:  0s
info: installing component 'cargo'
info: installing component 'clippy'
info: installing component 'rust-docs'
 18.3 MiB /  18.3 MiB (100 %)   3.4 MiB/s in  5s ETA:  0s
info: installing component 'rust-std'
 22.8 MiB /  22.8 MiB (100 %)   5.9 MiB/s in  3s ETA:  0s
info: installing component 'rustc'
 58.3 MiB /  58.3 MiB (100 %)   6.2 MiB/s in  9s ETA:  0s
info: installing component 'rustfmt'
info: default toolchain set to 'stable-mips64el-unknown-linux-gnuabi64'

  stable-mips64el-unknown-linux-gnuabi64 installed - rustc 1.62.0 (a8314ef7d 2022-06-27)


Rust is installed now. Great!

To get started you may need to restart your current shell.
This would reload your PATH environment variable to include
Cargo's bin directory ($HOME/.cargo/bin).

To configure your current shell, run:
source "$HOME/.cargo/env"
```
根据提示，需要为当前的 shell 激活 Rust 环境：
```
source ~/.profile
source ~/.cargo/env
```
之后可以使用下面的命令验证安装的 Rust 版本：
`rustc --version`
你将看到类似下面这张图的结果：
`rustc 1.62.0 (a8314ef7d 2022-06-27)`

## 更新及卸载
更新 Rust 非常简单，只需使用一个简单的命令即可完成：
`rustup update`
卸载 Rust，请运行以下命令：
`rustup self uninstall`
输入 Y 并按回车键即可。
