---
title: "Go 100 Mistakes: Data types"
date: 2023-05-28T16:01:23+08:00
lastmod: 2023-05-28T16:01:23+08:00
draft: false
tags: ["go"]
categories: ["develop"]
---

在这一章中，我们将探讨与基本类型、切片 `Slice` 和映射 `Map` 相关的常见错误。但是，关于字符串的内容将在专门讨论该特定数据类型的章节中介绍。

前面几个介绍了常见的整型溢出，浮点数计算 [IEEE754](https://zh.wikipedia.org/zh-hans/IEEE_754) 相关的内容，这些其实和 go 关系不大，这里不再进行赘述了。

## Slice 相关
### 不理解切片的长度和容量

### 对 nil 和 empty slices 感到疑惑

```go
func main() {
    var s []string

	log(1, s)

    s = []string(nil)
    log(2, s)

    s = []string{}
    log(3, s)

    s = make([]string, 0)
}
func log(i int, s []string) {
    fmt.Printf("%d: empty=%t\tnil=%t\n", i, len(s) == 0, s == nil)
}
```




