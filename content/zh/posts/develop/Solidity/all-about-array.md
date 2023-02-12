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

```js
// 固定长度
T[k]
// 动态长度
T[]
```

### 一维数组和多维数组

上面的例子其实是一维数组，多维数组本质上是嵌套数组，在 Solidity 中存在下面三种形式：

```js
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

### 数组字面常数（**Arrays literals**）

此种定义方式只能是固定大小的，数组声明中的第一个值必须进行类型转换。如下：

```js
// Here if k = 5, the array will contain 5 values.  
T[k] memory array_literal = [type(value_1), value_2, ... , value_k];// example  
string[3] memory rgb_colours = [string("red"), "green", "blue"];

// 另外一个例子
function func1() {  
    func2([uint(1), 2, 3]);
}
// memory type array that has a fixed size (uint3)  
function func2(uint[3] _data) {  
    // Some code here
}
```

请注意，我们不能将固定大小的内存数组分配给动态大小的内存数组。为了说明这一点，这里有一个无法编译的示例。

```js
function dontCompile() public {  
    uint[] memory x = [uint(1), 3, 4];  
}
// Remix Error:
/*TypeError: Type uint256[3] memory is not implicitly convertible to expected type uint256[] memory.         
uint[] memory x = [uint(1), 3, 4];           
^-------------------------------^*/
```

### 函数内部定义的数组

函数内部定义的数组在函数执行完成后会被释放掉，所以在函数内部定义的数组一定要加上关键字 memory，我们可以使用 new 关键字进行创建。

```js
function memoryArray(uint len) {  
      
    // memory array of length 7, containing uint  
    // x.length == 7  
    uint[] memory x = new uint[](7);  
      
    // memory array of length `len`, containing  bytes  
    // y.length == len  
    bytes memory y = new bytes(len);
}
```

针对内存数据还有一点是需要注意的：

> 我们不能通过为成员 `.length` 赋值来更改内存数组的大小，当尝试访问当前长度之外的元素时，这不会自动扩容。一旦创建，内存数组的大小是固定的（但是是动态的，因为它取决于运行时参数）。


## 数组的成员函数

1. .length：返回数组中包含的元素数，固定长度以及内存数组不会发生改变。
2. .push 在动态数组末尾追加一个新元素，并发挥数组的新ch

