---
date: Mar 28, 2023
difficulty: intro
title: "在 Rust 中设置、构建和部署 Solana 程序"
description:
  "这份快速入门指南将演示如何快速设置、构建并部署您的第一个基于Rust的Solana区块链程序。"
tags:
  - quickstart
  - local
  - rust
  - native
keywords:
  - rust
  - cargo
  - toml
  - program
  - tutorial
  - intro to solana development
  - blockchain developer
  - blockchain tutorial
  - web3 developer
altRoutes:
  - /developers/guides/local-rust-hello-world
---

Rust 是编写 Solana 程序最常用的编程语言。
这份快速入门指南将演示如何快速设置、构建和部署您的第一个基于 Rust 的 Solana 程序到区块链上。

<Callout type="caution" title="Do you have the Solana CLI installed?">
本指南使用 Solana CLI，并假定您已经设置好了本地开发环境。请查看我们的 [本地开发快速入门指南](./setup-local-development.md) 以快速完成设置。
</Callout>

## 你将会学到的内容

- 如何在本地安装 Rust 编程语言
- 如何初始化一个新的 Solana Rust 程序
- 如何使用 Rust 编写基本的 Solana 程序
- 如何构建和部署您的 Rust 程序

## 安装Rust和Cargo

为了能够编译基于 Rust 的 Solana 程序，请使用 [Rustup](https://rustup.rs/) 安装 Rust 编程语言和 Cargo（ Rust 包管理器）：


```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

## 运行本地验证节点

Solana CLI自带 [test validator](https://docs.solana.com/developing/test-validator)。
这个命令行工具允许您在本地运行一个完整的区块链集群。

```bash
solana-test-validator
```

<Callout title="PRO TIP">
在一个新的/独立的终端窗口中运行 Solana 测试验证节点，该窗口将保持打开状态。
这个命令行程序必须保持运行，以确保您的本地验证节点保持在线并随时准备就绪。
</Callout>

配置您的 Solana CLI，以在将来的所有终端命令和 Solana 程序部署时使用您的本地验证节点：

```bash
solana config set --url localhost
```

## 使用 Cargo 创建一个新的 Rust 库

使用 Rust 编写的 Solana 程序是编译为[BPF字节码](https://docs.solana.com/developing/on-chain-programs/faq#berkeley-packet-filter-bpf)并保存为`.so`格式的 _库_ 。

通过 Cargo 命令行初始化一个名为 `hello_world` 的新 Rust 库：

```bash
cargo init hello_world --lib
cd hello_world
```

将 `solana-program` crate 添加到您的新 Rust 库中：

```bash
cargo add solana-program
```

<Callout title="Pro Tip">
强烈建议将 `solana-program` 和其他 Solana Rust 依赖项与您安装的 Solana CLI 版本保持一致。

例如，如果您正在运行 Solana CLI `1.17.17`，您可以选择运行：

```
cargo add solana-program@"=1.17.17"
```

这将确保您的crate仅使用 `1.17.17`，而不是其他版本。
如果您在使用Solana依赖项时遇到兼容性问题，请查看 [Solana Stack Exchange](https://solana.stackexchange.com/questions/9798/error-building-program-with-solana-program-v1-18-and-cli-v1-17/9799)。

</Callout>

打开您的 `Cargo.toml` 文件，并添加以下必要的Rust库配置设置，根据需要更新您的项目名称：

```toml
[lib]
name = "hello_world"
crate-type = ["cdylib", "lib"]
```

## 创建你的第一个 Solana 程序

基于 Rust 的 Solana 程序的代码将存在于您的 `src/lib.rs` 文件中。在 `src/lib.rs` 中，您可以导入 Rust crate 并定义您的逻辑。

在您喜欢的编辑器中打开 `src/lib.rs` 文件。

在`lib.rs`的顶部，导入`solana-program` crate并将所需的项引入本地命名空间：


```rust
use solana_program::{
    account_info::AccountInfo,
    entrypoint,
    entrypoint::ProgramResult,
    pubkey::Pubkey,
    msg,
};
```

每个Solana程序都必须定义一个 `entrypoint`，告诉 Solana 运行时从何处开始执行您的链上代码。

您的程序的 [entrypoint](https://docs.solana.com/developing/on-chain-programs/developing-rust#program-entrypoint) 应该提供一个名为 `process_instruction` 的公共函数：


```rust
// declare and export the program's entrypoint
entrypoint!(process_instruction);

// program entrypoint's implementation
pub fn process_instruction(
    program_id: &Pubkey,
    accounts: &[AccountInfo],
    instruction_data: &[u8]
) -> ProgramResult {
    // log a message to the blockchain
    msg!("Hello, world!");

    // gracefully exit the program
    Ok(())
}
```

每个链上程序应该返回 `Ok` [result enum](https://doc.rust-lang.org/std/result/)，其值为 `()`。这告诉Solana运行时，您的程序在没有错误的情况下成功执行。

上述程序将简单地 [记录一条消息](https://docs.solana.com/developing/on-chain-programs/debugging#logging) "_Hello, world!_" 到区块链集群，然后以 `Ok(())` 优雅地退出。


## 构建你的Rust程序

在终端窗口中，您可以通过在项目的根目录（即包含您的 `Cargo.toml` 文件的目录）运行以下命令来构建您的 Solana Rust 程序：

```bash
cargo build-bpf
```

<Callout>

每次构建 Solana 程序后，上述命令将输出编译程序的 `.so` 文件的构建路径以及将用于程序地址的默认密钥文件。

`cargo build-bpf` 安装来自当前安装的 Solana CLI 工具的工具链。

如果遇到任何版本不兼容性，您可能需要升级这些工具。
</Callout>

## 部署你的Solana程序

使用 Solana CLI，您可以将您的程序部署到当前选择的集群：

```bash
solana program deploy ./target/deploy/hello_world.so
```

一旦您的Solana程序已部署（并且交易 [完成](https://docs.solana.com/cluster/commitments) ），上述命令将输出您的程序的公共地址（也称为其 “程序ID” ）。

```bash
# example output
Program Id: EFH95fWg49vkFNbAdw9vy75tM7sWZ2hQbTTUmuACGip3
```

#### 恭喜！

您已成功设置、构建并部署了一个使用Rust语言的Solana程序。

<Callout type="success" title="Check your wallet balance!">
在部署后再次检查您的Solana钱包余额。看看部署您的简单程序花费了多少SOL？
</Callout>

## 接下来的步骤

请查看以下链接，了解有关编写基于 Rust 的 Solana 程序的更多信息：

- [编写 Solana 程序概述](https://docs.solana.com/developing/on-chain-programs/overview)
- [使用 Rust 开发 Solana 程序的更多信息](https://docs.solana.com/developing/on-chain-programs/developing-rust)
- [链上程序调试](https://docs.solana.com/developing/on-chain-programs/debugging)
