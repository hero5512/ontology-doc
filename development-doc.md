## ontology开发文档

### 快速入门



### 网络配置

| Ontology MainNet |      |
| ---------------- | ---- |
| NetworkName      |      |
| chainId          |      |
| Gas Token        | ONG  |
| RPC              |      |
| Websocket        |      |
| Block Explorer   |      |

### 合约部署

本体目前支持neovm，wasm和evm虚拟机（其中neovm和wasm虚拟机部署教程请参考[本体开发文档]([https://dev-docs.ont.io/#/docs-cn/introduction/tools])），本文将着重讲解如果使用以太坊Remix，Truffle等相关工具在Ontology上开发和部署智能合约。

#### [Remix IDE](https://remix.ethereum.org/).

1. Remix环境初始化

   如果你首次使用remix那么你应该在remix添加两个模块：Solidity Compiler 和 Deploy and Run Transactions。

   在PLUGIN MANAGER里面寻找Solidity Compiler 和 Deploy and Run Transactions并添加到自己的编译器中。

   ![image-20210526142630046](./image-20210526142630046.png)

   选择solidity环境，创建HelloWorld.sol合约，将HelloWorld.sol合约复制到该文件中。

   ![image-20210526143301031](./image-20210526143301031.png)

   ```js
   // Specifies that the source code is for a version
   // of Solidity greater than 0.5.10
   pragma solidity ^0.5.10;
   
   // A contract is a collection of functions and data (its state)
   // that resides at a specific address on the Ethereum blockchain.
   contract HelloWorld {
   
       // The keyword "public" makes variables accessible from outside a contract
       // and creates a function that other contracts or SDKs can call to access the value
       string public message;
   
       // A special function only run during the creation of the contract
       constructor(string memory initMessage) public {
           // Takes a string value and stores the value in the memory data storage area,
           // setting `message` to that value
           message = initMessage;
       }
   
       // A publicly accessible function that takes a string as a parameter
       // and updates `message`
       function update(string memory newMessage) public {
           message = newMessage;
       }
   }
   ```

2. 编译智能合约

   - 点击Solidity Compiler按钮，选择编译器版本为0.5.10，编译HelloWorld.sol。
   - 现在我们可以将合约部署到Ontology网络中，在部署合约之前，我们需要将metamask连接到本体网络。
   - 选择自定义RPC网络，输入网络名字“Ontology TestNet”，在URL输入本体的RPC地址“”，Chain ID选择“”，最后保存我们输入的配置。
   - 去本体Faucet地址领取ONG。
   - 现在我们可以将HelloWorld合约部署到Ontology网络上。
   - 在Environment中选择Injected Web3选项，点击deploy完成合约部署。
   - ![RemixIDE_Step1](./rpc.png)

#### Truffle

开发环境初始化，受限安装truffle环境允许需要的依赖。

- [Node.js v8+ LTS and npm](https://nodejs.org/en/) (comes with Node)
- [Git](https://git-scm.com/)

使用如下命令安装truffle

```js
npm install -g truffle
```

完成truffle-config的配置，选择truffle-config.js更新该文件。

```js
const HDWalletProvider = require('@truffle/hdwallet-provider');
const fs = require('fs');
const mnemonic = fs.readFileSync(".secret").toString().trim();

module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",     // Localhost (default: none)
      port: 8545,            // Standard Ethereum port (default: none)
      network_id: "*",       // Any network (default: none)
    },
    ontology: {
      provider: () => new HDWalletProvider(mnemonic, `todo`),
      network_id: 80001,
      confirmations: 2,
      timeoutBlocks: 200,
      skipDryRun: true
    },
  },

  // Set default mocha options here, use special reporters etc.
  mocha: {
    // timeout: 100000
  },

  // Configure your compilers
  compilers: {
    solc: {
    }
  }
}
```

你需要在项目的根目录创建.secret文件，将你部署合约使用账户的助记词复制到该文件（账户的助记词可以在metamask里面找到）。

运行如下命令部署合约

```
$ truffle migrate --network matic
```

显示如下输出则代表部署成功

```
2_deploy_contracts.js
=====================

   Replacing 'MyContract'
   ------------------
   > transaction hash:    0x1c94d095a2f629521344885910e6a01076188fa815a310765679b05abc09a250
   > Blocks: 5            Seconds: 5
   > contract address:    0xbFa33D565Fcb81a9CE8e7a35B61b12B04220A8EB
   > block number:        2371252
   > block timestamp:     1578238698
   > account:             0x9fB29AAc15b9A4B7F17c3385939b007540f4d791
   > balance:             79.409358061899298312
   > gas used:            1896986
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH

   Pausing for 2 confirmations...
   ------------------------------
   > confirmation number: 5 (block: 2371262)
initialised!

   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

### 开发工具

### 钱包使用

#### Metamask

Metamask是一个用户用于使用自己设定密码管理以太坊钱包私钥的插件，它是一个非托管的钱包，这意味着用户有权限自己管理自己的私钥，一旦丢失该私钥用户将无法恢复对钱包的使用。

**Type**: Non-custodial/HD
**Private Key Storage**: User’s local browser storage
**Communication to Ethereum Ledger**: Infura
**Private key encoding**: Mnemonic

1. 初始化 Web3

   Step 1:

   在你的DApp内安装web3环境:

   ```
   npm install --save web3
   ```

   生成一个新的文件，命名为 `web3.js` ，将以下代码复制到该文件:

   ```js
   import Web3 from 'web3';
   
   const getWeb3 = () => new Promise((resolve) => {
     window.addEventListener('load', () => {
       let currentWeb3;
   
       if (window.ethereum) {
         currentWeb3 = new Web3(window.ethereum);
         try {
           // Request account access if needed
           window.ethereum.enable();
           // Acccounts now exposed
           resolve(currentWeb3);
         } catch (error) {
           // User denied account access...
           alert('Please allow access for the app to work');
         }
       } else if (window.web3) {
         window.web3 = new Web3(web3.currentProvider);
         // Acccounts always exposed
         resolve(currentWeb3);
       } else {
         console.log('Non-Ethereum browser detected. You should consider trying MetaMask!');
       }
     });
   });
   
   
   export default getWeb3;
   ```

   简言之，只要你在你的Chrome浏览器里安装了Metamask插件，你就可以使用该插件注入的`ethereum`全局变量。

   Step 2:

   在你的client里引入如下代码,

   ```js
   import getWeb3 from '/path/to/web3';
   ```

   调用如下函数:

   ```js
     getWeb3()
       .then((result) => {
         this.web3 = result;// we instantiate our contract next
       });
   ```

2. 获取账户

   我们需要从以上创建的web3实例中获取一个账户来发送交易。

   ```js
     this.web3.eth.getAccounts()
     .then((accounts) => {
       this.account = accounts[0];
     })
   ```

   `getAccounts()` 函数 返回了metamask中的账户, `accounts[0]` 是当前用户的.

3. 初始化你的合约

4. 调用函数

   现在你可以使用你刚才创建的合约实例调用任何你想调用的函数

   注: - 你可以使用 `send()` 函数调用合约来改变合约状态

   ​	 - 调用 `call()` 函数完成合约的预执行操作

**Calling `call()` Functions**

```js
  this.myContractInstance.methods.myMethod(myParams)
  .call()
  .then (
    // do stuff with returned values
  )
```

**Calling `send()` Functions**

```js
  this.myContractInstance.methods.myMethod(myParams)
  .send({
    from: this.account,gasPrice: 0
  })
  .then (
    (receipt) => {
      // returns a transaction receipt}
    )
```

#### Onto Web Wallet

TODO

#### API 参考

由于以太坊与本体交易的结构体和存储结构存在差异，目前本体只支持了以太坊部分RPC接口（ontology部分接口返回的内容与以太坊返回的有所不同），具体如下：

TODO

### L2方案集成



### 其他Dapp集成

本体支持所有以太坊的开发工具，您可以轻松的使用以太坊工具在本体上开发和部署基于evm的合约。

