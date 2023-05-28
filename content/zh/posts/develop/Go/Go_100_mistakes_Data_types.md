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

## 1. Slice 相关
### 1.1 不理解切片的长度和容量
### 1.2 对 nil 和 empty slices 感到疑惑
```go
func main() {
    var s []string
    log(1, s)

    s = []string(nil)
    log(2, s)

    s = []string{} // 被推荐用于创建带有初始元素的切片
    log(3, s)

    s = make([]string, 0) // 在需要生成已知长度的切片的情况下，建议使用 make 的方式 s := make([]string, length)
    log(4, s)
}
func log(i int, s []string) {
    fmt.Printf("%d: empty=%t\tnil=%t\n", i, len(s) == 0, s == nil)
}
```

上面的输出结果是：

1: empty=true nil=true
2: empty=true nil=true
3: empty=true nil=false
4: empty=true nil=false

其实这里我们也可以看出来，如果想判断一个切片是否为空，可以使用 len 函数进行判断，而不能通过判断是否为 nil，因为 nil 的 slice 长度也是 0，但有两点需要注意：
* nil切片和空切片之间的主要区别之一涉及内存分配。初始化一个nil切片不需要进行任何内存分配，而对于空切片则不然。
* 无论切片是nil还是空，调用append内置函数都可以正常工作。例如，

```go
var s1 []string
fmt.Println(append(s1, "foo")) // [foo]
```

### 1.3 错误的切片复制
copy 内置函数允许将源切片的元素复制到目标切片中。虽然它是一个方便的内置函数，但是 Go 开发人员有时会误解它。让我们看一个常见的错误，导致复制错误数量的元素。

在下面的示例中，我们创建了一个切片，并将其元素复制到另一个切片中。这段代码的输出应该是什么？

```go
src := []int{0, 1, 2}
var dst []int // correct: dst := make([]int, len(src))
copy(dst, src)
fmt.Println(dst)
```

如果我们运行这个示例，它会打印出 []，而不是 [0 1 2]。我们错过了什么？

要有效地使用 copy 函数，重要的是要理解复制到目标切片的元素数量取决于以下两者中的最小值：
- 源切片的长度
- 目标切片的长度

在上面的示例中，源切片 `src` 的长度是 3，但目标切片 `dst` 的长度是零，因为它被初始化为其零值。因此，copy 函数会复制最小数量的元素（在 3 和 0 之间取最小值为 0），导致结果切片为空。
