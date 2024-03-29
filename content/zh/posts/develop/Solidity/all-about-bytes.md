---
title: "有关 Solidity bytes 的一切"
date: 2023-02-12T16:01:23+08:00
lastmod: 2023-02-12T16:01:23+08:00
draft: false
tags: ["blockchain","Solidity"]
categories: ["develop"]
authors: 
- "stoneworld"
---

## Solidity 中的字节顺序和字节数据布局

我们可以先在 [字节顺序](https://zh.wikipedia.org/zh-hans/%E5%AD%97%E8%8A%82%E5%BA%8F) 了解下计算机的字节顺序的基础知识，如果你对此不了解的话。ETH 的 EVM 是采取的 Big Endian 格式的虚拟机。在 EVM 中，所有数据（无论其 Solidity 类型如何）都以大端方式存储在虚拟机内部的低层。最重要的是，EVM 使用 32 字节字来处理数据。但不同的是，数据类型决定了其数据填充方式：

* 右侧填充： `string` 、 `bytes` 和 `bytesN` 
-  左填充： `intN` / `uintN` （有符号/无符号整数）、 `address` 和其他类型

例如，EVM 将 Solidity 中的 `string value = “abcd”` 填充为完整的 32 字节字。它会被填充在右边。

> 0x6162636400000000000000000000000000000000000000000000000000000000

相反，EVM 将 Solidity 中的数字 uint256 value = 0x61626364 (十六进制) 填充为完整的 32 字节字。它会被填充在左边：

> 0x0000000000000000000000000000000000000000000000000000000061626364

学习数据布局和填充的规则对于了解如何在 Solidity 中处理不同的数据类型很重要。尤其是在处理 `bytesN` 和他们的 `uintN` 时。例如对于 `bytes1` 和 `uint8` 值，即使它们具有相同的位大小，它们的内部表示也是不同的。请参阅下面的代码片段。

```js

// 0x00000000…01  
uint8 a = 1;__
// 0x01000000….  
byte b = 1;  

```

本文主要关注 `bytesN` 和 `bytes` 类型。 Solidity 提供两种字节类型：

* 固定大小的字节数组： `bytesN`
* 动态大小的字节数组： `bytes` 表示字节序列。

## 固定大小的字节数组

您可以使用关键字 `bytesX` 来定义变量，其中 `X` 表示字节序列。 `X` 可以是 1 到 32，其中 `byte` 是 `bytes1` 的别名，因此存储单个字节。

> 如果您可以将长度限制为一定数量的字节，请始终使用 bytes1 到 bytes32 中的一个，因为它们便宜得多。

具有固定大小变量的字节可以在合约之间传递。

众所周知，以太坊中的地址是一个 20 字节的值（例如： `0xa59b89aee4f944a04d8fc075967d616b937dd4a7` ）。因此，可以通过下面的方式进行转换。
```js
address public my_address;// This function is really expensive the 1st time   
// it's called (21 000 gas). Why ?  
function bytesToAddress(bytes20 input) public returns (address) {  
    my_address = address(input);  
    return my_address;  
}
```


## 动态大小的字节数组

Solidity 中的术语 `bytes` 表示一个动态的字节数组。这是 `byte[]` 的简写，但 `byte[]` 类型是一个字节数组，但是由于填充规则，它为每个元素浪费了 31 个字节的空间（存储空间除外）。最好改用 `bytes` 类型。

原文链接：https://jeancvllr.medium.com/solidity-tutorial-all-about-bytes-9d88fdb22676

参考：[以太中的位操作和位运算](https://medium.com/@imolfar/bitwise-operations-and-bit-manipulation-in-solidity-ethereum-1751f3d2e216)