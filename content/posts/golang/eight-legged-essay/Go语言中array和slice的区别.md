---
title: "Go 语言中 array 和 slice 的区别"
date: 2024-03-07T17:48:55+08:00
draft: false
description: "Go 语言中 array 和 slice 的区别。"

tags:
  - "Go切片面试问题合集"
categories:
  - "Go面试"

featuredImage: "/posts/golang/eight-legged-essay/images/golang-ms.png"
featuredImagePreview: "/posts/golang/eight-legged-essay/images/golang-ms.png"

toc:
  enable: true
  auto: true
---

<!--more-->

## Go 语言中 array 和 slice 的区别

### 1、array

有固定大小的就是数组

```go
var a [10]int
```

### 2、slice

无固定大小的就是切片

```go
var a []int
```

### 3、两者区别

| 特性     | 数组(array)         | 切片(slice)                                   |
| ------ | ----------------- | ------------------------------------------- |
| 大小     | 固定大小，定义时指定。       | 动态大小，可以改变。                                  |
| 类型     | 值类型。复制数组会复制其全部元素。 | 引用类型。复制切片仅复制指向底层数组的引用。                      |
| 声明     | var a [n]Type     | var s []Type 或者 s := make([]Type, len, cap) |
| 零值     | 元素类型的零值构成的数组。     | nil                                         |
| 内部结构   | 单纯的连续内存块。         | 包含指针、长度和容量的描述符指向底层数组。                       |
| 内存分配   | 栈分配（如果是局部变量）。     | 堆分配（动态增长时）。                                 |
| 性能     | 大型数组可能导致较大的复制开销。  | 复制效率高，因为仅复制切片描述符。                           |
| 用法     | 适用于已知固定数量元素的场景。   | 适用于需要动态大小的场景，如动态数组。                         |
| 容量操作   | 不可改变。             | 可以使用内置的 append 函数动态增加大小。                    |
| 底层数据修改 | 直接修改数组元素。         | 通过切片间接修改底层元素的元素。                            |
| 时间复杂度  | 计算数组长度 O(n)。      | 计算数组长度 O(1)。                                |