---
title: "Go 方法与函数的区别"
date: 2024-03-04T16:47:46+08:00
draft: false
description: "Go 方法与函数的区别。"

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

## Go 方法与函数的区别

在Go 语言中，函数和方法不太一样，有明确的概念区分。其他语言中，比如Java，一般来说函数就是方法，方法就是函数。

### 1、方法

```go
func (t *T) add(a, b int) int {
    return a + b
}
```

其中T是自定义类型或者结构体，不能是基础数据类型int等。

### 2、函数

```go
func add(a, b int) int {
    return a + b
}
```

### 3、两者区别

| 特性   | 方法(Method)                | 函数(Function)          |
| ---- | ------------------------- | --------------------- |
| 关联对象 | 与特定类型（如结构体、类型别名）关联。       | 独立于类型，可以在包的任何地方定义。    |
| 接受者  | 有接收者，表示方法属于并可访问该类型的实例或指针。 | 没有接收者，仅定义参数列表和返回值。    |
| 调用方式 | 通过类型的实例或者指针调用。            | 直接使用函数名和参数列表调用。       |
| 定义位置 | 必须在一个具体的类型定义内部。           | 可以在包的任何位置定义，不依赖于特定类型。 |