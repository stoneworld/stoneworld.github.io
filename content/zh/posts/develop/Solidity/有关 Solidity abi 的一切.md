---
title: "有关 Solidity abi 的一切"
date: 2018-03-03T16:01:23+08:00
lastmod: 2018-03-03T16:01:23+08:00
draft: false
tags: ["blockchain","Solidity"]
categories: ["develop"]
---


## 智能合约 ABI（Application Binary Interface）[all about ABI](https://coinsbench.com/solidity-tutorial-all-about-abi-46da8b517e7)

### ABI 与字节码

智能合约一旦部署在网络上，它的字节码会与地址关联起来，并存储在区块链上。更准确地说，智能合约的字节码存储在智能合约地址的“code”字段下的全局世界状态中，智能合约不能主动发起调用，只能被动等待调用，这里我们可以用 Javascript 获取下 `UniswapV3Factory` 智能合约（[Deployment Addresses | Uniswap](https://docs.uniswap.org/contracts/v3/reference/deployments)）的字节码：

```js
const Web3 = require("web3");
const provider = "YOUR_INFURA_OR_QUICKNODE_HTTP_ENDPOINT";  
const web3 = new Web3(provider);
const UniswapV3Factory = "0x1F98431c8aD98523631AE4a59f267346ea31F984";
web3.eth.getCode(UniswapV3Factory).then(console.log);
> 0x608060405234801561001057600080fd5b506004361061007d5760003560e01c8063890357301161005b578063890357301461013b5780638a7c195f146101855780638da5cb5b146101b0578063a1671295146101b85761007d565b806313af4035146100825780631698ee82146100aa57806322afcccb14610102575b600080fd5b6100a86004803603602081101561009857600080fd5b50356001600160a01b03166101f4565b005b6100e6600480360360608110156100c057600080fd5b5080356001600160a01b03908116916020810135909116906040013562ffffff16610267565b604080516001600160a01b039092168252519081900360200190f35b6101246004803603602081101561011857600080fd5b503562ffffff16610293565b6040805160029290920b8252519081900360200190f35b6101436102a8565b604080516001600160a01b0396871681529486166020860152929094168383015262ffffff16606083015260029290920b608082015290519081900360a00190f35b6100a86004803603604081101561019b57600080fd5b5062ffffff813516906020013560020b6102de565b6100e66103a1565b6100e6600480360360608110156101ce57600080fd5b5080356001600160a01b03908116916020810135909116906040013562ffffff166103b0565b6003546001600160a01b0316331461020b57600080fd5b6003546040516001600160a01b038084169216907fb532073b38c83145e3e5135377a08bf9aab55bc0fd7c1179cd4fb995d2a5159c90600090a3600380546001600160a01b0319166001600160a01b0392909216919091179055565b60056020908152600093845260408085208252928452828420905282529020546001600160a01b031681565b60046020526000908152604090205460020b81565b600054600154600280546001600160a01b03938416939283169281169162ffffff600160a01b83041691600160b81b9004900b85565b6003546001600160a01b031633146102f557600080fd5b620...
```

可以把智能合约的字节码想象成它的“大脑”。它以机器代码的形式描述合约逻辑。该机器代码是通过将高级智能合约编程语言（如 Solidity）编译为可执行机器语言而获得的：EVM 字节码。

>上面的 EVM 字节码只不过是用十六进制编写的 EVM 操作码序列。

这就给我们留下了第一个问题：可执行代码是机器代码。它不是人可读的，而只能是机器可读的。只有一个特殊的机器（即以太坊虚拟机）才能理解它，并知道如何执行它。这对 EVM 来说都很好。但是对于人类来说，合约字节码不提供任何上下文（就像 C++ 文件的二进制可执行文件）。
我们遇到的第二个问题是除非你开始与智能合约交互，它们才可以触发并执行它们的逻辑，而且使智能合约在区块链上可执行的是它们的实际字节码。

> 那么我们怎么给到智能合约一份人类可读、机器可执行的的交互方式呢？

ABI 正是解决这一问题的方法，官方描述是：ABI 是以太坊生态系统中与合约互动的标准方式，无论是来自区块链之外，还是用于合约之间的交互。

ABI 中的 "I" 代表 Interface（接口），通过将给定的输入转换为 EVM 的“机器可读”格式，它使得可以与合约的 EVM 字节码进行通信。将 ABI 视为一种便于人类理解的智能合约描述方法。智能合约的 ABI 将描述其公共接口以及如何与其交互。它定义了可以调用的函数，并保证函数将以您期望的格式返回数据。

### ABI = 编码与解码的规范

我们之前已经看到，ABI 是在客户端（直接来自 EOA 或接口）和智能合约字节码（EVM 操作码中的合约逻辑）之间创建交互链接的工具，然而，ABI 不仅仅是这两个层面（人和 EVM）之间的联系。最重要的是，ABI 定义了如何编码和解码数据以及合约调用的明确规范，在以太坊和任何基于 EVM 的链中，ABI 基本上是为 EVM 编码合约调用的方式（以便 EVM 理解要运行哪些指令）。反之亦然，ABI 指定了如何从交易中读取和解码数据，因为所有在交易中指定的数据都被编码为原始十六进制数据，因此，ABI 也是将数据编码为机器码或从机器码解码数据的方法，后面我们将在单独的章节中了解 ABI 如何对不同类型的数据进行编码和解码。


## 了解合约的 JSON ABI

通常对于 Dapp 接口，Solidity 智能合约的 ABI 表示为一个对象数组。每个对象可以对应于：

-   合约中可以公开调用的方法（函数）（= 可以由任何人调用，除非它附带有限制性修饰符）。
-   事件定义
-   fallback() 或 receive() 函数。

在 truffle 或 hardhat 中编译 Solidity 合约时，您可以在生成的 .json 工件中找到 ABI。这个 JSON 文件将包含名为“abi”的字段，下面是 UniswapV3Factory 的一个基本示例：

```json
{
"abi": [
    {
      "inputs": [],
      "stateMutability": "nonpayable",
      "type": "constructor"
    },
    {
      "anonymous": false,
      "inputs": [
        {
          "indexed": true,
          "internalType": "uint24",
          "name": "fee",
          "type": "uint24"
        },
        {
          "indexed": true,
          "internalType": "int24",
          "name": "tickSpacing",
          "type": "int24"
        }
      ],
      "name": "FeeAmountEnabled",
      "type": "event"
    },
    // events definition of `OwnerChanged`, `PoolCreated`
    {
      "inputs": [
        {
          "internalType": "address",
          "name": "tokenA",
          "type": "address"
        },
        {
          "internalType": "address",
          "name": "tokenB",
          "type": "address"
        },
        {
          "internalType": "uint24",
          "name": "fee",
          "type": "uint24"
        }
      ],
      "name": "createPool",
      "outputs": [
        {
          "internalType": "address",
          "name": "pool",
          "type": "address"
        }
      ],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "uint24",
          "name": "fee",
          "type": "uint24"
        },
        {
          "internalType": "int24",
          "name": "tickSpacing",
          "type": "int24"
        }
      ],
      "name": "enableFeeAmount",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    // function definitions of:
    //     - `feeAmountTickSpacing(...)`
    //     - `getPool(...)`
    //     - `owner()` 
    //     - `parameters(...)`
    //     - `setOwner(...)`
  ]
}
```

### 函数的 JSON ABI 规范

如我们所看到的，ABI 有助于进一步规范化合约，它通过参数列表及其类型显示可以调用哪些函数以及如何调用。让我们详细了解一下 JSONABI 中 `function` 和 `event` 的属性和规范。
-   **type:**  `function` 、 `constructor` 、 `event` 、 `receive` （用于接收以太功能）或 `fallback` （用于默认回退功能）
-   **name**: 函数的名称。
-   **inputs**: 函数参数，作为具有每个参数的名称和类型的数组。
-   **outputs**: 函数返回值的数组。
-   **stateMutability**:  `view` 、 `pure` 、 `payable` 或 `nonpayable`，这还涉及函数是只读的还是可以写入合约状态。

> 注意：对于 `constructor` 和 `fallback` 函数，JSON ABI 中的名称和输出字段为空。对于 `fallback` 函数，输入总是空的，因为回退函数不能接受参数。

`"inputs"` 字段包含对象数组，每个对象定义函数参数。

-   **type**: 参数的基本类型（例如： `uint256` 、 `address` 等）。
-   **outputs**: 它是一个类似于输入的输出对象数组。

如果参数是元组（如用户定义的 `struct` ），则 `“input"` 字段将包含名为 **components** 的字段。如果在该字段内存在另一元组，则将其定义为 `“type”: “tuple”`，示例如下：

```solidity
pragma solidity ^0.4.19;
pragma experimental ABIEncoderV2;

contract Test {
  struct S { uint a; uint[] b; T[] c; }
  struct T { uint x; uint y; }
  function f(S s, T t, uint a) public { }
  function g() public returns (S s, T t, uint a) {}
}

```

Json 如下：

``` json
[
  {
    "name": "f",
    "type": "function",
    "inputs": [
      {
        "name": "s",
        "type": "tuple",
        "components": [
          {
            "name": "a",
            "type": "uint256"
          },
          {
            "name": "b",
            "type": "uint256[]"
          },
          {
            "name": "c",
            "type": "tuple[]",
            "components": [
              {
                "name": "x",
                "type": "uint256"
              },
              {
                "name": "y",
                "type": "uint256"
              }
            ]
          }
        ]
      },
      {
        "name": "t",
        "type": "tuple",
        "components": [
          {
            "name": "x",
            "type": "uint256"
          },
          {
            "name": "y",
            "type": "uint256"
          }
        ]
      },
      {
        "name": "a",
        "type": "uint256"
      }
    ],
    "outputs": []
  }
]
```

### JSON ABI Event 规范

-   **type:** 始终为 `event` 。
-   **name:** 事件的名称。
-   **inputs:** 可传递给事件的参数数组。
-   **anonymous:**  `true` ，如果事件在 Solidity 代码中声明为 [anonymous]( https://docs.soliditylang.org/en/v0.8.13/contracts.html?highlight=anonymous#events ) 。否则为 ` false ` ，如果事件声明为匿名，`topics[0]` 则不会生成，所以如果是 `anonymous` 则可以有四个 indexed

与函数一样，JSON ABI 中事件的"input"字段包含一个对象数组，表示事件参数。每个对象由以下属性组成：

-   **name**: 参数的名称。
-   **type**: 参数的基本类型（例如： `uint256` 、 `address` 等）。
-   **indexed**: 如果字段是日志主题的一部分，则为 `true` ; 如果字段是日志数据段之一，则为 `false` 。


## Solidity ABI -编码数据


我们之前讨论过并看到 ABI 定义了 Solidity 数据类型应该如何编码，以便它们可以传递给 EVM 由其解释，Solidity 中的大多数静态类型，如 `address` 、uint256 或 bytes32，都被编码为 32 字节。字节填充因底层 Solidity 类型而异。例如， `address` 在左侧补零，而小于 32 字节的固定大小字节值（如 `bytes4` 、 `bytes8` 、 `bytes20` 等）在右侧补零。

### abi. encode

Solidity 内置函数 `abi.encode` 允许将任何 Solidity 类型编码为原始字节，EVM 可以直接解释这些原始字节，调用例子如下：

```js
abi.encode("Solidity")

// 返回值如下：

0x00000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000000000000000000000000000000004536f6c6964697479000000000000000000000000000000000000000000000000
// Split in words 32 bytes long  
0x0000000000000000000000000000000000000000000000000000000000000020  
  0000000000000000000000000000000000000000000000000000000000000004  
  536f6c6964697479000000000000000000000000000000000000000000000000
```

针对字符串类型：始终使用以下格式进行编码：

-   第 1 个（32 字节）=偏移 指示字符串从哪个字节索引开始。此处 0x20（十六进制）= 32。如果从开始数 32，则将到达实际编码字符串开始的起始点。
-   第二个（32字节）字符串长度→在字符串的情况下，这表示字符串中包含多少个字符（包括空格）。所以简单的“ `string.length` “
-   第 3 个（32 字节）实际的 utf8 编码字符串→每个单独的字节对应于以 utf8 编码的字母/字符的十六进制符号。如果在 utf8 表中搜索 `536f6c6964697479` 中的每个字节，就可以解码该字符串。例如， `53` 对应于大写的 `S` ， `6f` 对应于小写的 `o` ， `6c` 对应于小写的 `l` ，等等。

### abi. encodePacked

Solidity 通过内置函数 `abi.encodePacked(...)` 为数据提供了一种非标准编码模式。这使得能够以原始字节对数据进行编码，而无需遵循 ABI 指定的约定。

在 Solidity 中进行压缩编码时，将有以下 ABI 规则：

-   动态类型（例如：字符串、数组、......）按原样编码，没有偏移量或长度。
-   小于 32 字节的静态类型（例如：Uint8、字节 4 等）不进行零填充。

同样的，这里的例子如下：

```js
abi.encode("Solidity")  
> 0x00000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000000000000000000000000000000004536f6c6964697479000000000000000000000000000000000000000000000000

abi.encodePacked("Solidity")  
> 0x536f6c6964697479
```

从上面可以看到， `abi.encodePacked(...)`  删除了字符串偏移量和长度，只返回字符串的 utf8 表示形式。


## Solidity ABI -编码合约调用

函数 `abi.encodeWithSignature(...)` 和 `abi.encodeWithSelector(...)` 可以在 Solidity 中使用，为外部合约调用准备原始的 `payloads` 作为合约调用的参数。参数可以传递到低级 Solidity `.call (...) ` 、 `.delegatecall (...) ` 和 `.staticcall (...) ` 函数，这两个函数将从第二个参数开始对传递的参数进行编码。

```
abi.encodeWithSelector(bytes4 selector, ...) returns (bytes memory)
```

对于此函数，必须提供要调用的函数的 `bytes4` 函数选择器。选择器由函数签名的 Keccak 256 hash 的前四个字节组成，获取函数选择器有以下几种方式：

-  如果引用的是合约类型的变量，请使用语法 `someContract.someFunction.selector` 。
-  您可以直接引用 `interface` 的函数选择器，类似  `IERC20.transfer.selector`
-  您可以从其签名字符串生成 bytes4 散列，语法如下：

```js
bytes4 selector = bytes4(keccak256("transfer(address,uint256)"));
```

当然也可以直接直接调用 `abi.encodeWithSignature` 如下：

```js
abi.encodeWithSignature("transfer(address,uint256)", to, amount); 
abi.encodeWithSignature(string memory signature, ...) returns (bytes memory)
等价于
abi.encodeWithSelector(bytes4(keccak256(bytes(signature))), ...)
```

也可以看下下 [UniswapV2Pair](https://github.com/Uniswap/v2-core/blob/4dd59067c76dea4a0e8e4bfdda41877a6b16dedc/contracts/UniswapV2Pair.sol#L16) 的合约：

```js
bytes4 private constant SELECTOR = bytes4(keccak256(bytes('transfer(address,uint256)')));

function _safeTransfer(address token, address to, uint value) private {
	(bool success, bytes memory data) = token.call(abi.encodeWithSelector(SELECTOR, to, value));
	require(success && (data.length == 0 || abi.decode(data, (bool))), 'UniswapV2: TRANSFER_FAILED');
}
```

## 将 Solidity 映射到 ABI 类型

Solidity 中可用的大多数基本类型都是 ABI 规范的一部分。这包括 `address` 、 `bytes32` 等，但是，某些特定的 Solidity 类型在 ABI 中没有直接对应项。然后将这些转换为 ABI 中定义的类型。
![demo](/image1.jpg)

![](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*f4jYe6T6VSXPYruhjssL2w.png)

简而言之，类型 `address payable` 或 `contract` 的变量将作为标准 `address` 由 ABI 在幕后编码/解码，类似 `struct`，ABI 将 `struct` 编码为基本类型的元组，自 Solidity 版本  `>0.8.0` 以来，这一点已发生变化，因为 `enum` 的成员数不能超过 256 个。


## 参考文献

1. [What are ABI encoding functions in Solidity 0.4.24? | by libertylocked | Medium](https://medium.com/@libertylocked/what-are-abi-encoding-functions-in-solidity-0-4-24-c1a90b5ddce8)
2. [evm - What is an ABI and why is it needed to interact with contracts? - Ethereum Stack Exchange](https://ethereum.stackexchange.com/questions/234/what-is-an-abi-and-why-is-it-needed-to-interact-with-contracts)
3. [What is an ABI? explained - step-by-step beginners guides | QuickNode](https://www.quicknode.com/guides/solidity/what-is-an-abi)
4. [Explaining Ethereum Contract ABI & EVM Bytecode | by eiki | Medium](https://medium.com/@eiki1212/explaining-ethereum-contract-abi-evm-bytecode-6afa6e917c3b)
5. [Solidity by Example](https://solidity-by-example.org/abi-encode/)



