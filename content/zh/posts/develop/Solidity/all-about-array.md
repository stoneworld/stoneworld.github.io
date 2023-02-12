---
title: "有关 Solidity 数组 的一切"
date: 2023-02-12T16:01:23+08:00
lastmod: 2023-02-12T16:01:23+08:00
draft: false
tags: ["blockchain","Solidity"]
categories: ["develop"]
authors: 
- "stoneworld"
---

在 Solidity 中，array 是一个有序的列表，索引从 0 开始，


## 数组类型

### 固定长度数组和可变长度数组

下面的示例中， T 为元素类型， K 数数组长度。

```
// 固定长度
T[k]
// 动态长度
T[]
```

### 一维数组和多维数组

上面的例子其实是一维数组，多维数组本质上是嵌套数组，在 Solidity 中存在下面三种形式：

```
T[k][k] // 二维固定长度
T[][] // 二维动态长度
T[][K] 或 T[k][] // 二维 混合长度
// 当然还会存在 三维 四维...
```

但有以下注意点：

* 嵌套数组不能有不同的类型
* 嵌套数组的最大嵌套级别是 15

## 定义数组

1. 针对固定长度： string[4] players_room
2. 针对动态长度：sring[] have_voted
3. 二维数组1：string[2]\[] crypto_names
4. 二维数组2：string[]\[6] names_A_to_F

它和其他语言不同，类似 string[2]\[] 这里的 2 指的是 固定大小为 2 的数组，这一点和其他语言是不一致的，这里可以把前面的 string[2] 看成一个类型，表示后面的数组是包含两个 string 的数组类型。

对于固定长度的数组是没有 .push 方法的，这一点也需要注意。

