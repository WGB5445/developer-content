---
date: Jan 18, 2023
difficulty: intro
featured: true
featuredPriority: 0
title: "Solana开发入门（仅使用浏览器）"
seoTitle: "Introduction to Solana development"
description:
  "Solana 开发者快速入门指南，学习如何在 Solana Playground 中仅使用浏览器设置、构建和部署你的第一个 Solana 程序。"
tags:
  - quickstart
  - Solana Playground
  - native
  - rust
  - web3js
keywords:
  - playground
  - solana pg
  - on chain
  - rust
  - native program
  - tutorial
  - intro to solana development
  - blockchain developer
  - blockchain tutorial
  - web3 developer
altRoutes:
  - /developers/guides/hello-world-in-your-browser
  - /developers/guides/solana-playground
---

对于这个 "Hello World" 快速入门指南，我们将使用 [Solana Playground](https://beta.solpg.io)，这是一个基于浏览器的集成开发环境（IDE），用于开发和部署我们的 Solana 程序。使用它时，你**无需**在计算机上安装任何软件。只需在你选择的浏览器中打开 Solana Playground，你就可以准备好编写和部署 Solana 程序。


## 你将学到什么

- 如何开始使用 Solana Playground
- 如何在 Playground 上创建 Solana 钱包
- 如何使用 Rust 编写基本的 Solana 程序
- 如何构建和部署 Solana Rust 程序
- 如何使用 JavaScript 与你的链上程序进行交互

## 使用 Solana Playground

[Solana Playground](https://beta.solpg.io) 是一个基于浏览器的应用程序，允许你在链上编写、构建和部署Solana程序，一切都在你的浏览器中完成，无需安装。


这是一个在 Solana 开发中入门的绝佳资源，特别是在 Windows 系统上。

### 导入我们的示例项目

在浏览器的新标签页中，打开我们的示例 "Hello World" [Solana Playground 项目](https://beta.solpg.io/6314a69688a7fca897ad7d1d)

接下来，通过点击 "**导入**" 图标，并将你的项目命名为 `hello_world`，将项目导入到你的本地工作区。

<!-- ![Import the get started Solana program on Solana Playground](/img/quickstarts/solana-get-started-import-on-playground.png) -->

> 如果你**不**将程序导入**你的** Solana Playground，那么你将**无法**对代码进行更改。但你仍然可以构建和部署代码到 Solana 集群。

### 创建一个Playground钱包

通常，使用[本地开发](/content/guides/getstarted/setup-local-development.md)时，你需要创建一个文件系统钱包以供 Solana CLI 使用。但是在 Solana Playground 中，你只需要点击几下按钮就可以创建一个基于浏览器的钱包。

> 你的 _Playground 钱包_ 将保存在浏览器的本地存储中。清除浏览器缓存将删除你保存的钱包。在创建新钱包时，你将有选择保存你钱包的密钥对文件的选项。

点击屏幕左下角的红色状态指示器按钮，（可选）将你的钱包密钥对文件保存到计算机上进行备份，然后点击"**继续**"。

创建完你的 Playground 钱包后，你会注意到窗口底部现在显示了你的钱包地址、你的 SOL 余额以及你连接到的 Solana 集群（ Devnet 通常是默认/推荐的，但也可以选择 "localhost" [测试验证器](/content/guides/getstarted/setup-local-development.md)）

## 创建一个 Solana 程序

基于 Rust 的 Solana 程序的代码将存放在你的 `src/lib.rs` 文件中。在 `src/lib.rs` 中，你可以导入 Rust 的 crate 并定义你的逻辑。在 Solana Playground 中打开你的 `src/lib.rs` 文件。

### 导入 `solana_program` crate

在 `lib.rs` 的顶部，我们导入 `solana-program` crate 并将所需的项引入到本地命名空间中：

```rust
use solana_program::{
    account_info::AccountInfo,
    entrypoint,
    entrypoint::ProgramResult,
    pubkey::Pubkey,
    msg,
};
```

### 编写你的程序逻辑

每个 Solana 程序都必须定义一个 `entrypoint`，告诉 Solana 运行时在链上代码的哪里开始执行。你的程序的 [entrypoint](/docs/programs/lang-rust.md#program-entrypoint) 应该提供一个名为 `process_instruction` 的公共函数：

```rust
// 声明并导出程序的入口点
entrypoint!(process_instruction);

// 程序入口点的实现
pub fn process_instruction(
    program_id: &Pubkey,
    accounts: &[AccountInfo],
    instruction_data: &[u8]
) -> ProgramResult {
    // 在区块链上记录一条消息
    msg!("Hello, world!");

    // 优雅地退出程序
    Ok(())
}
```

每个链上程序都应该返回 `Ok` [result enum](https://doc.rust-lang.org/std/result/)，其值为`()`。这告诉 Solana 运行时，你的程序已成功执行且没有错误。

我们上面的程序将简单地在区块链集群中 [记录一条消息](/docs/programs/debugging.md#logging) "_Hello, world!_"，然后以 `Ok(())` 优雅地退出。

### 构建你的程序

在左侧边栏中，选择 "**Build & Deploy**" 选项卡。接下来，点击"构建"按钮。

如果你查看 Playground 的终端，你应该能看到你的 Solana 程序开始编译。一旦完成，你将看到一个成功的消息。


<!-- ![Viewing a successful build of your Rust based program](/img/quickstarts/solana-get-started-successful-build.png) -->

> 在编译程序时，由于未使用的变量，你可能会收到 _警告_。不用担心，这些警告不会影响你的构建。这是因为我们的非常简单的程序没有使用在 `process_instruction` 函数中声明的所有变量。

### 部署你的程序

你可以点击 "部署" 按钮将你的第一个程序部署到 Solana 区块链，具体到你选择的网络（例如，Devnet、Testnet等）。

每次部署后，你都会看到 Playground 钱包余额发生变化。默认情况下，Solana Playground 将自动代表你请求 SOL 空投，以确保你的钱包有足够的 SOL 来支付部署的成本。

> 注意：如果你需要更多的 SOL，你可以在 Playground 终端中输入 airdrop 命令进行空投：

```sh
solana airdrop 2
```

<!-- ![Build and deploy your Solana program to the blockchain](/img/quickstarts/solana-get-started-build-and-deploy.png) -->

### 查找你的 program ID

在使用 [web3.js](/docs/clients/javascript-reference.md) 或 [另一个 Solana 程序](/docs/core/cpi.md) 执行程序时，你需要提供 `program id`（也称为 program 的地址）。

在 Solana Playground 的 **Build & Deploy** 侧边栏中，你可以在 **Program Credentials** 下拉菜单中找到你的 `program id`。


#### 恭喜！

你已成功在浏览器中直接使用 Rust 语言设置、构建和部署了一个 Solana 程序。接下来，我们将演示如何与你的链上程序进行交互。

## 与你的链上程序交互

一旦你成功将 Solana 程序部署到区块链上，你会希望与该程序进行交互。

与大多数创建 dApps 和网站的开发人员一样，我们将使用 JavaScript 与我们的链上程序进行交互。具体来说，我们将使用开源的 [NPM package](https://www.npmjs.com/package/@solana/web3.js) `@solana/web3.js` 来辅助我们的客户端应用程序。

> 这个 web3.js 包是在[JSON RPC API](/docs/rpc.md)之上的一个抽象层，减少了重复编写代码，有助于简化代码开发。


### 初始化客户端

我们将使用 Solana Playground 生成客户端。通过在 Playground 终端中运行 `run` 命令来创建一个客户端文件夹：

```bash
run
```

我们已经创建了 `client` 文件夹和一个默认的 `client.ts`。这是我们在接下来的 "hello world" 程序中将要工作的地方。

### Playground 全局变量

在 Playground 中，有许多实用工具是全局可用的，无需安装或设置任何内容。对于我们的 "hello world" 程序，最重要的是 `@solana/web3.js` 的 `web3` 和 Solana Playground 工具的 `pg`。

> 你可以在编辑器内按下 `CTRL + SPACE`（或在 macOS 上按下 `CMD + SPACE`）查看所有可用的全局变量。


### 调用程序

要执行你的链上程序，你必须发送一个[交易](/docs/core/transactions.md)到它。提交到 Solana 区块链的每个交易都包含一系列的指令（以及将与该指令进行交互的程序）。

在这里，我们创建了一个新的交易，并向其中添加了一个单独的`指令`：

```js
// 创建一个空的交易
const transaction = new web3.Transaction();

// 向交易中添加一个 hello world 程序的指令
transaction.add(
  new web3.TransactionInstruction({
    keys: [],
    programId: new web3.PublicKey(pg.PROGRAM_ID),
  }),
);
```

每个`指令`都必须包含操作涉及的所有密钥和我们要执行的程序ID。在这个例子中，`keys` 是空的，因为我们的程序只是记录了 `hello world`，不需要任何账户。

我们创建的交易，可以将其提交到 RPC:

```js
// 提交交易到 Solana RPC
console.log("Sending transaction...");
const txHash = await web3.sendAndConfirmTransaction(
  pg.connection,
  transaction,
  [pg.wallet.keypair],
);
console.log("Transaction sent with hash:", txHash);
```

> signers 数组中的第一个签名者默认是交易的手续费支付者。我们使用密钥对 `pg.wallet.keypair` 进行签名。

### 运行应用程序

编写完客户端应用程序后，你可以通过相同的 `run` 命令运行代码。

一旦你的应用程序完成，你将看到类似于以下输出：

```sh
Running client...
  client.ts:
    My address: GkxZRRNPfaUfL9XdYVfKF3rWjMcj5md6b6mpRoWpURwP
    My balance: 5.7254472 SOL
    Sending transaction...
    Transaction sent with hash: 2Ra7D9JoqeNsax9HmNq6MB4qWtKPGcLwoqQ27mPYsPFh3h8wignvKB2mWZVvdzCyTnp7CEZhfg2cEpbavib9mCcq
```

### 获取交易日志

我们将在 Playground 中直接使用 `solana-cli` 获取有关任何交易的信息：

```sh
solana confirm -v <TRANSACTION_HASH>
```

将 `<TRANSACTION_HASH>` 替换为你从调用 `hello world` 程序中收到的哈希。

你应该在输出的**日志消息**部分看到 `Hello, world!`。 🎉

#### 恭喜!!!

你现在已经为你的链上程序编写了一个客户端应用程序。你现在是一名 Solana 开发人员！

PS：尝试更新你程序的消息，然后重新构建、重新部署和重新执行你的程序。

## 下一步

查看以下链接，了解有关编写 Solana 程序和设置本地开发环境的更多信息：

- [设置本地开发环境](/content/guides/getstarted/setup-local-development.md)
- [Solana 程序编写概述](/docs/programs/index.md)
- [使用 Rust 开发 Solana 程序的更多信息](/docs/programs/lang-rust.md)
- [链上程序调试](/docs/programs/debugging.md)