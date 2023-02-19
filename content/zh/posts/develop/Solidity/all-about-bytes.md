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

我们可以先在 [字节顺序](https://zh.wikipedia.org/zh-hans/%E5%AD%97%E8%8A%82%E5%BA%8F) 了解下计算机的字节顺序的基础知识，如果你对此不了解的话。ETH 的 EVM 是采取的 Big Endian 格式的虚拟机。在 EVM 中，所有数据（无论其 Solidity 类型如何）都以大端方式存储在虚拟机内部的低层。最重要的是，EVM 使用 32 字节字来处理数据。