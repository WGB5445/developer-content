---
date: Jul 29, 2023
difficulty: intro
title: "如何获取 Solana 开发网络 devnet 的 SOL（包括空投和水龙头）"
seoTitle: "Faucets: How to get Solana devnet SOL"
description:
  "获取 Solana 开发和测试网络 （ devnet and testnet ）SOL 的最常见方式包括：airdrop、web3.js、POW 水龙头等。"
tags:
  - faucet
keywords:
  - faucet
  - blockchain
  - devnet
  - testnet
---

# 如何获取 Solana 的开发网络（devnet）SOL代币（包括空投和水龙头）

下面是开发者在 Solana 开发网络（devnet）和测试网络（testnet）上获取 SOL 的不同途径。

## 1. Solana Airdrop

_在 Devnet 和 Testnet 上可用_

这是获取 SOL 的基本方式，但在 airdrop 请求次数较多时可能会受到速率限制。

以下是使用此方法请求空投的三种不同方式：

### 使用 Solana CLI:

```bash
solana airdrop 2
```

### 使用 web3.js:

```js
const connection = new Connection("https://api.devnet.solana.com");
connection.requestAirdrop();
```

查看更多:
[`requestAirdrop()`](https://solana-labs.github.io/solana-web3.js/classes/Connection.html#requestAirdrop)
在 web3.js 文档中的相关说明

## 2. 网络水龙头

_在 Devnet 上可用_

1. [faucet.solana.com](https://faucet.solana.com) - 由 Solana Foundation 托管的公共网络水龙头
2. [SolFaucet.com](https://solfaucet.com/) - 用于从公共RPC端点进行空投的 Web 用户界面
3. [QuickNode](https://faucet.quicknode.com/solana/devnet) - 由 QuickNode 运营的网络水龙头

_在 Testnet_ 上可用_

1. [faucet.solana.com](https://faucet.solana.com) - 由Solana Foundation托管的公共网络水龙头
2. [SolFaucet.com](https://solfaucet.com/) - 用于从公共RPC端点进行空投的Web用户界面
3. [QuickNode](https://faucet.quicknode.com/solana/testnet) - 由QuickNode运营的网络水龙头
4. [TestnetFaucet.org](https://testnetfaucet.org) - 由[@Ferric](https://twitter.com/ferric)运营的网络水龙头，其速率限制与公共RPC端点分开

## 3. RPC Providers 水龙头

_在 Devnet 上可用_

RPC Providers 可以选择通过开发网络验证节点分发开发网络的 SOL。

<Callout>

如果您是 RPC 提供者并希望分发 SOL，请[在此联系我们](https://c852ena8x5c.typeform.com/to/cUj1iRhS)。

</Callout>

当前支持的：

1. [Helius](https://www.helius.dev/)
2. [QuickNode](https://faucet.quicknode.com/solana/devnet)
3. [Triton](https://triton.one/)

### 使用 Solana 命令行界面（CLI）

指定您的 [clusters](https://docs.solana.com/clusters) 作为您的 RPC provider 的 URL。

```bash
solana config set --url <your RPC url>
```

然后，您可以像本指南中的第一选项一样请求空投。

```bash
solana airdrop 2
```

### 使用 Web3.js

```js
const connection = new Connection("your RPC url");
connection.requestAirdrop();
```

## 4. 工作证明 水龙头

_在 Devnet 上可用_

这是一个工作证明水龙头，通过您的计算能力，可以向您分发 devnet 的 SOL。

**安装 devnet 的 POW Crate：**

```bash
cargo install devnet-pow
```

**开始挖掘 devnet SOL**

```bash
devnet-pow mine
```

_⚠️ [POW Faucet](https://github.com/jarry-xiao/proof-of-work-faucet) 由 Ellipsis Labs 维护。_

## 5. Discord 水龙头

一些 Discord 社群已经设置了 BOT 分发 devnet SOL。

| Community      | Usage                                                       | Link                                         |
| -------------- | ----------------------------------------------------------- | -------------------------------------------- |
| The 76 Devs    | 在 BOT commands 频道执行 `!gibsol`.                  | [加入服务器](https://discord.gg/RrChGyDeRv) |
| The LamportDAO | 在 BOT commands 频道执行 `/drop <address> <amount>`. | [加入服务器](https://discord.gg/JBVrJgtFkq) |

## 6. 重复利用 devnet SOL

最可持续的保存 SOL 的方式是进行重复使用。
使用 Solana CLI，您可以通过以下命令显示并关闭所有先前的缓冲账户：

```bash
solana program show --buffers
```

<Callout title="What's a buffer account?">

在部署程序时，缓冲账户会自动创建。在部署过程中，程序的所有数据都会转移到该账户中。一旦完成，程序的数据将被新数据替换。

</Callout>

通常，这些缓冲账户会自动关闭。如果它们没有自动关闭，您可以使用以下命令手动关闭它们，以便收回其中的 SOL：

```bash
solana program close <buffer account>
```

您还可以使用 `show` 子命令显示您已经部署到当前选择集群的所有 program：

```bash
solana program show --programs
```

然后，您可以使用 `close` 子命令关闭每个程序，以关闭它们并检索您用于部署它们的 SOL：

```bash
solana program close <program id>
```

然后，您可以使用那些 SOL 来部署新的程序。

<Callout type="caution">

一旦关闭了程序，您将**无法**再次使用相同的程序ID。因此，请确保关闭的是正确的程序，并且您将不再需要该 ID。

如果您受到 Solana CLI 配置使用的 RPC 端点的速率限制，您可以在这些命令中的任何一个后添加 `-u "urlToYourRpc"`，以使用不同的RPC端点。

</Callout>
