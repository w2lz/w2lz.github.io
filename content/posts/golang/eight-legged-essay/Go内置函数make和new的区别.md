---
title: "Go 内置函数 make 和 new 的区别"
date: 2024-03-05T13:49:15+08:00
draft: false
description: "Go 内置函数 make 和 new 的区别。"

tags:
  - "Go基础面试问题合集"
categories:
  - "Go面试"

featuredImage: "/posts/golang/eight-legged-essay/images/golang-ms.png"
featuredImagePreview: "/posts/golang/eight-legged-essay/images/golang-ms.png"

toc:
  enable: true
  auto: true
---

<!--more-->

## Go 内置函数 make 和 new 的区别

在 Go 语言中，make 和 new 是两个用语分配内存的内置函数，但是他们的用途和行为有着明显的区别：

### 1、new

new(T)为任意类型分配零值内存，并返回其地址，即一个*T类型的值。它仅分配内存，不初始化内存，所分配的内存被初始化为类型的零值。

```go
num := new(int) // 分配内存，*num 初始化为 0
```

使用 new 基础类型后可以直接使用，使用 new 切片等引用类型后需要额外的初始化。

### 2、make

make 仅用于切片（slice）、映射（map）和通道（channel）这三种引用类型的内存分配和初始化。make(T, args)返回初始化后的（非零）值，而不是指针。对于切片，make 还可以接受长度和容量参数。

```go
s := make([]int, 10) // 创建一个长度为 10 的切片，元素初始化为 0
```

### 3、两者的区别

make(T)只能用语初始化 slice（切片）、map（映射）和 channel（通道）这三种引用类型的数据结构，它不仅分配内存，还对其进行初始化。

new(T)为任意类型分配内存，并返回指向这个新分配的零值的指针，它适用于所有类型的变量（如基本类型、数组、结构体等），并返回一个指向新分配的零值的变量的指针。分配的变量被初始化为零值。对于数值类型，零值是 0；对于指针类型，零值是 nil。

| 特性   | new          | make                         |
| ---- | ------------ | ---------------------------- |
| 返回类型 | 指针（*T）       | 引用类型的实例（如 slice、map、channel） |
| 用途   | 为类型分配零值内存    | 分配并初始化切片、映射、通道               |
| 初始化  | 分配的内存被初始化为零值 | 分配的内存被初始化为非零值                |
| 返回类型 | 任何类型         | 仅限切片、映射、通道                   |

在实际使用中，选择 new 还是 make 通常取决于你需要的是指针还是已初始化的引用类型。