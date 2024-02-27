# wagmi_v1.0与Viem简介



- wagmi_v1.0更新内容及对比

- wagmi_v1.0简要总结

- Viem简要介绍

  

## wagmi_v1.0更新内容及对比

### 主要

#### 移除了`ethers`依赖

现版本将ethers移除，使用了  [`viem`](https://wagmi.sh/react/migration-guide#1xx-breaking-changes) 替代

#### *Provider* & *Signer* 调整

```js
import { WagmiConfig, createConfig } from 'wagmi'
- import { getDefaultProvider } from 'ethers'
+ import { createPublicClient, http } from 'viem'

const config = createConfig({
   autoConnect: true,
-  provider: getDefaultProvider(),
+  publicClient: createPublicClient({
+  	chain: mainnet,
+  	transport: http()
+ })
})
```

例如:

`configureChains` :

​	`chain`初始化解构内容调整

​	移除 `provider`、`webSocketProvider` 添加 `publicClient`、`webSocketPublicClient`

`createConfig`:

​	初始化wagmi config 参数调整

​	移除 `provider`、`webSocketProvider` 添加 `publicClient`、`webSocketPublicClient`

#### 部分方法改名

```js
- import { createClient } from 'wagmi'
+ import { createConfig } from 'wagmi'
 
- const client = createClient({
+ const config = createConfig({
  ...
})
```

### 钩子

#### `useContract`

*钩子移除*

#### `useSigner`

更名为**`getWalletClient`**

现在返回的是`viem`的 [Wallet Client](https://viem.sh/docs/clients/wallet.html) ，不再是`ethers`的`Signer`了。

```js
- const { data } = useSigner(...)
//        ^? const data: Signer
+ const { data } = useWalletClient(...)
//        ^? const data: WalletClient
```

#### `useContractWrite`

删除了“准备”模式的要求。现在可以使用 `useContractWrite`，而无需先调用 `usePrepareContractWrite`。

![image-20231117151053692](/Users/xiaonahe/Library/Application Support/typora-user-images/image-20231117151053692.png)

```js
      //	v0.12
import { useContractWrite, usePrepareContractWrite } from 'wagmi'
 
function App() {
  const { config } = usePrepareContractWrite({
    address: '0xecb504d39723b0be0e3a9aa33d646642d1051ee1',
    abi: wagmigotchiABI,
    functionName: 'feed',
  })
  const { data, isLoading, isSuccess, write } = useContractWrite(config)
 
  return (
    <div>
      <button disabled={!write} onClick={() => write?.()}>
        Feed
      </button>
      {isLoading && <div>Check Wallet</div>}
      {isSuccess && <div>Transaction: {JSON.stringify(data)}</div>}
    </div>
  )
}
       // recklesslySetUnpreparedArgs
       //	v1.0
       
import { useContractWrite } from 'wagmi'
 
function App() {
  const { data, isLoading, isSuccess, write } = useContractWrite({
    address: '0xecb504d39723b0be0e3a9aa33d646642d1051ee1',
    abi: wagmigotchiABI,
    functionName: 'claim',
  })
 
  return (
    <div>
      <button
        disabled={!write}
        onClick={() =>
          write({
            args: [69],
            from: '0xA0Cf798816D4b9b9866b5330EEa46a18382f251e',
            value: parseEther('0.01'),
          })
        }
      >
        Claim
      </button>
      {isLoading && <div>Check Wallet</div>}
      {isSuccess && <div>Transaction: {JSON.stringify(data)}</div>}
    </div>
  )
}
```

#### `useSignTypedData`

对消息结构进行了改进，更接近EIP-712的格式

```json
// EIP-712
{
  "types": {
    "EIP712Domain": [
      { "name": "name", "type": "string" },
      { "name": "version", "type": "string" },
      { "name": "chainId", "type": "uint256" },
      { "name": "verifyingContract", "type": "address" }
    ],
    "Person": [
      { "name": "name", "type": "string" },
      { "name": "wallet", "type": "address" }
    ]
  },
  "primaryType": "Person",
  "domain": {
    "name": "Example",
    "version": "1",
    "chainId": 1,
    "verifyingContract": "0x0000000000000000000000000000000000000000"
  },
  "message": {
    "name": "Alice",
    "wallet": "0x1234567890123456789012345678901234567890"
  }
}

```

```js
//	v0.12
useSignTypedData({
      domain,
      types,
      value,
})
//	v1.0
//	将value更名为message、并添加primaryType字段
useSignTypedData({
  domain,
  types,
  primaryType: 'Mail',
  message
})
```



## wagmi_v1.0简要总结

### 概括

该版本最大的改动，就是移除了`ethers`依赖，并引入了`Viem`来替代它。

### 为什么需要viem？

 wagmi团队：

​		当前以太坊接口抽象的现状在以下四个方面至少存在一个不足：**开发者体验**、**稳定性**、**捆绑包大小**、**性能** 四个问题。	

​		wagmi 是一款流行的用于以太坊的 React Hooks 库，我们在使用现有的低级 TypeScript 以太坊库时遇到了困难。我们希望为 wagmi 的用户提供最佳的开发者体验，但受到 wagmi 构建所依赖的基础技术的限制。我们知道，始终稳定、可预测的实现，以及小型捆绑包大小和高性能的模块对于与全球最大的区块链生态系统进行交互至关重要。

因此，我们创建了**viem**：以太坊的 TypeScript 接口，它提供了用于与以太坊交互的低级无状态原语。ethers.js 和 web3.js 的替代方案，专注于可靠性、效率和出色的开发人员体验。





## Viem

### 什么是viem？

这是一个用于以太坊的 TypeScript 接口，提供了用于与以太坊进行交互的低级无状态基元。viem 是 ethers.js 和 web3.js 的替代品，专注于可靠性、效率和出色的开发者体验。

### viem、web3.js、ethers对比

构建 Web 应用程序时，保持较小的包大小至关重要。最终用户不应被要求下载超过 100kB 的模块才能与以太坊交互。在慢速 3G 移动网络上加载 100kB 库至少需要**两秒**（加上建立 HTTP 连接的额外时间）。

而viem是按需引入的，只有你使用的模块才会出现在你最终的包中。

![img](https://viem.sh/bench-bundlesize.svg)

### viem Clients

#### Public Client

提供对公共操作的访问，可执行通过公共操作检索区块号、交易、读取智能合约等。例如 getBlockNumber 和 getBalance。

```js
import { createPublicClient, http } from 'viem'
import { mainnet } from 'viem/chains'

const client = createPublicClient({ 
  chain: mainnet,
  transport: http()
})


const blockNumber = await client.getBlockNumber() 
```

#### Wallet Client

提供对钱包操作的访问，提供通过钱包操作检索账户、执行交易、签署消息等的能力。例如sendTransaction和signMessage。

```js
import { createWalletClient, custom } from 'viem' 
import { mainnet } from 'viem/chains'

const client = createWalletClient({
  chain: mainnet,
  transport: custom(window.ethereum)
})

const [address] = await client.getAddresses() 
```

#### Test Client

提供对测试操作的访问，用于“测试”JSON-RPC API 方法，可通过本地以太坊测试节点（例如 Anvil 或 Hardhat）访问，例如通过测试操作挖掘区块、模拟帐户、设置费用等。

```js
import { createTestClient, http, publicActions, walletActions } from 'viem'
import { foundry } from 'viem/chains'

const client = createTestClient({
  chain: foundry,
  mode: 'anvil',
  transport: http(), 
})
  .extend(publicActions)
  .extend(walletActions)

const blockNumber = await client.getBlockNumber() // Public Action
const hash = await client.sendTransaction({ ... }) // Wallet Action
const mine = await client.mine({ blocks: 1 }) // Test Action
```

