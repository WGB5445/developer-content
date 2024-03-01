---
featured: true
date: Feb 26, 2024
difficulty: intro
title: "搭建本地开发环境并安装Solana命令行工具"
description:
  "轻松在 Linux、Mac 或 Windows 上配置您的 Solana 开发本地环境，包括安装 Rust、Solana 命令行工具和 Anchor。"
tags:
  - quickstart
  - linux
  - mac
  - windows
  - wsl
  - local
  - cli
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
  - /developers/guides/setup-local-development
---

本快速入门指南将演示如何快速安装并设置您的本地开发环境，使您能够开始开发并部署 Solana 程序到区块链。

## 您将学到的内容

- 如何在本地安装 Solana 命令行工具
- 如何设置本地 Solana 集群/验证器
- 如何创建用于开发的 Solana 本地钱包
- 如何为您的钱包进行 SOL 代币的空投

<Callout>

如果您对开发比较陌生，或者只是尝试过基于以太坊虚拟机（EVM）的区块链开发，那么您的计算机可能还未准备好 Solana 编码环境。

</Callout>

本指南主要涵盖三个操作系统：Windows（使用WSL）、Linux 和 MacOS。
在这三个操作系统之间，进行本地设置的基本步骤基本相同：

1. 安装依赖项
2. 安装 Rust 和 Cargo 工具链
3. 安装 Solana 命令行工具
4. 安装 Anchor
5. 设置本地区块链验证者
6. 创建本地文件系统钱包
7. 向您的钱包空投 Solana 代币


## 1. 安装依赖项

虽然 Rust 能够编译并构建软件为指定的计算机架构生成可运行的二进制文件，但我们仍然需要在计算机上安装一些操作系统级别的依赖项。

### Windows 系统的依赖项

您可以通过使用 WSL（Windows Subsystem for Linux）在 Windows 上开始使用 Solana。
WSL 允许您在 Windows 上轻松运行 Linux 软件，使用一个轻量级的虚拟机，在需要时即时启动。

#### 配置 WSL 以进行 Solana 开发

首先开始 [安装 WSL](https://learn.microsoft.com/zh-cn/windows/wsl/install)  到您的系统。安装完成后，请确保重新启动计算机，然后继续阅读本指南

```bash
wsl --install
```

在安装 WSL 并重新启动计算机后，使用 WSL 打开新的 Linux 终端会话：

```bash
wsl
```

在本指南的剩余部分和您使用 WSL 进行 Solana 开发的过程中，您将在这个 Linux 终端内运行所有命令、Solana 构建和程序部署（除非在本指南中另有说明）。

如果您选择使用 VS Code 作为您的代码编辑器，我们建议您 [按照此教程](https://code.visualstudio.com/docs/remote/wsl-tutorial) 在 VS Code 网站上，以正确配置 VS Code 和 WSL。这将为您提供最佳的开发者体验。


<Callout type="caution" title="Notice">

在下面关于为 Solana 开发设置 WSL 的部分之后，Windows / WSL 用户应该继续按照本指南中的 Linux 步骤进行操作，除非另有说明。

</Callout>

在您的 Linux / WSL 终端会话中，继续按照下面的 “Linux” 步骤设置本地 Solana 开发环境。

由于 WSL 的文件系统写入速度限制，WSL 有时可能会有些慢。您也可以尝试在计算机上进行双启动，直接在同一台机器上安装 Linux 操作系统，或者使用 [Solana Playground](https://beta.solpg.io) 的基于完全网页浏览器的 Solana IDE。

### Linux 系统的依赖项

在您的 Ubuntu / Debian Linux 系统上安装以下依赖项：

```bash
sudo apt-get install -y \
    build-essential \
    pkg-config \
    libudev-dev llvm libclang-dev \
    protobuf-compiler libssl-dev
```

### macOS 系统的依赖项

在 macOS 中，构建工具由 [Xcode 命令行](https://developer.apple.com/xcode/resources/) 工具提供，您可以直接从 Apple 下载。
您可能需要使用您的 Apple ID 登录下载。

您可以通过以下命令检查 Xcode CLI 是否已安装：

```bash
xcode-select -p
```

如果您没有看到返回的路径，说明您需要安装 CLI 工具。

#### 有三种安装 Xcode CLI 工具的方法：

1. 通过终端使用以下命令进行安装：

```bash
xcode-select --install
```

2. 下载安装程序并通过图形界面进行安装 
   [Apple Developer Tools](https://developer.apple.com/download/all/)

   ![Xcode CLI from Apple](/assets/guides/setup-local-environment/setup-xcode.png)

3. 通过 Homebrew 进行安装：请参考以下指南
   [install Xcode Command Line Tools with Homebrew](https://mac.install.guide/commandlinetools/3)

<Callout type="success" title="Congrats">

您已经安装了 Solana 程序开发所需的系统依赖项和构建基础组件。

</Callout>

## 2. 安装 Rust

Rust 编程语言是一种多范式、通用的编程语言，强调性能、类型安全和并发性。

使用 [rustup](https://rustup.rs/)，官方 Rust 版本安装器和管理器，我们将一次性安装 `rustc`（rust 的编译器）和 `cargo`（rust 的包管理器）。

#### 在 macOS、Linux、WSL 或其他类Unix操作系统上安装 Rust

使用以下命令，我们可以在您的本地系统上安装和配置 Rust 工具。
以下命令将自动下载适用于您特定操作系统所需的正确二进制文件：

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
```

作为 Rust 安装程序的一部分，Rustup 会将配置您终端的 `PATH`，以包含 Rust 工具链。

安装完成后，重新启动您的终端，或者运行以下命令手动刷新新的 `PATH` 设置，以使 Rust 工具（如 `cargo`）可用：


```bash
source ~/.bashrc
```

## 3. 安装 Solana 命令行工具

对于本地开发，包括编译 Solana 程序，您需要 [安装 Solana 命令行工具](https://docs.solanalabs.com/cli/install)。
这个命令行工具套件提供了执行常见任务所需的所有命令，例如：

- 创建和管理本地 Solana 钱包/密钥对，
- 连接到 Solana [集群端点](/docs/core/clusters.md)，
- 构建 Solana 程序，
- 部署您的程序到区块链。

#### 适用于 Linux、macOS、WSL 或其他类 Unix 系统:

1.  使用官方安装命令安装 Solana CLI 工具套件:

    ```bash
    sh -c "$(curl -sSfL https://release.solana.com/stable/install)"
    ```

2.  您可以将 `stable` 替换为与您期望的版本软件版本匹配的发布标签（例如 `v1.18.1`），或使用三个通道名称之一：`stable`、`beta` 或 `edge`。

3.  根据您的具体操作系统，Solana CLI 安装程序的消息可能会提示您更新 `PATH` 环境。

    ```bash
    Please update your PATH environment variable to include the Solana programs:
    ```

    如果您收到上述消息，请简单地复制并粘贴由 Solana CLI 安装程序推荐的命令来更新您的 PATH 环境变量。

    运行此命令后，请重新启动您的终端，以确保您的 Solana 二进制文件在之后打开的所有终端会话中都可以访问。

4.  要检查安装是否成功，请检查 Solana CLI 的版本:

    ```bash
    solana --version
    ```

您可以根据目标查看更多版本和发布
[solana/releases](https://github.com/solana-labs/solana/releases)

<Callout title="Updating the Solana CLI">


在将来，您可以使用 Solana CLI 自行更新到最新版本：`solana-install update`

</Callout>

## 4. 安装用于 Solana 的 Anchor 框架

[Anchor](https://www.anchor-lang.com/) 是 Solana 运行时的一个框架，为编写链上程序提供了几个方便的开发工具。

它帮助您写更少的代码，因为它使用 [Rust 宏](https://doc.rust-lang.org/book/ch19-06-macros.html) 将许多安全检查和常见样板抽象化。

为了安装和管理 Anchor 的版本，我们将使用 `avm`，即 anchor 版本管理器。

由于 `avm` 是通过 `cargo`（Rust 的包管理器）安装的，所以安装步骤对所有操作系统都相同。

然后，我们可以使用 `avm` 安装所需版本的 Anchor 框架。

#### 安装 avm（Anchor 版本管理器）

```bash
cargo install --git https://github.com/coral-xyz/anchor avm --locked --force
```

#### 使用 avm 安装 Anchor

要使用 `avm` 安装 `latest` 版本的 Anchor：

```bash
avm install latest
avm use latest
```

安装 Anchor 完成后，您可以通过检查已安装的版本来验证是否安装成功：

```bash
anchor --version
```

如果您没有看到输出或收到错误信息，您可能需要重新启动终端。

## 5. 设置本地区块链集群

Solana CLI 自带 [test validator](https://docs.solanalabs.com/cli/examples/test-validator)。

这个命令行工具将允许您在您的机器上运行一个完整的区块链集群：


```bash
solana-test-validator
```

<Callout type="success" title="Pro Tip">

在一个 新的/独立 的终端窗口中运行 Solana 测试验证器，并保持窗口保持打开状态。

此命令行程序必须保持运行，以使您的本地主机集群保持在线并准备处理交易和请求（如部署程序）。

</Callout>

配置您的 Solana CLI 以在将来的所有终端命令中使用您的本地主机验证器：

```bash
solana config set --url localhost
```

随时可以查看您当前的 Solana CLI 配置设置：

```bash
solana config get
```

## 6. 创建一个本地钱包

要使用 Solana CLI 部署程序，您将需要一个带有 SOL 代币的 Solana 钱包，用于支付区块链上交易和数据存储的费用。

让我们创建一个在本地开发期间使用的简单本地钱包：

```bash
solana-keygen new
```

默认情况下，`solana-keygen` 命令将创建一个位于 `~/.config/solana/id.json` 的新本地钱包。

您可以使用 `--outfile /path` 选项手动指定输出文件位置。


<Callout type="note">

如果您已经在默认位置保存了一个本地钱包，此命令**不会**覆盖它，除非您明确使用 `--force` 标志进行强制覆盖。


</Callout>

### 将您的新钱包设置为默认

创建了新的本地钱包后，您必须告诉 Solana CLI 使用此钱包来部署并控制您的链上程序：

```bash
solana config set -k ~/.config/solana/id.json
```

## 7. 向您的钱包空投 SOL 代币

一旦将您的新钱包设置为默认钱包，您就可以请求向其进行免费的 SOL 代币空投：

```bash
solana airdrop 2
```

<Callout type="caution">

`solana airdrop` 命令对每个集群（测试网或开发网）的每次空投请求都有 SOL 代币的数量限制。如果您的空投交易失败，请降低您的空投请求数量，然后重试。

</Callout>

您可以随时检查您当前钱包的 SOL 余额:

```bash
solana balance
```

## 接下来的步骤

请查看以下链接，了解有关编写基于 Rust 的 Solana 程序的更多信息：

- [创建和部署 Solana Rust 程序](./local-rust-hello-world.md)
- [编写 Solana 程序概述](/docs/programs/index.md)