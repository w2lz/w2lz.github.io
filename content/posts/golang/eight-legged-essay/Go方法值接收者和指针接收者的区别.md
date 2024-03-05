---
title: "Go 方法值接收者和指针接收者的区别"
date: 2024-03-04T18:16:21+08:00
draft: false
description: "Go 方法值接收者和指针接收者的区别。"

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

## Go 方法值接收者和指针接收者的区别

在Go语言中，方法可以有两种类型的接收者：值接收者（Value Receiver）和指针接收者（Pointer Receiver）。这两种接收者在方法定义和行为上有一些关键的区别。

### 1、值接收者

当方法使用值接收者时，它在每次方法调用时都会获取接收者的一个副本。这意味着方法对接收者的任何修改都不会影响原始对象。值接收者适用于小型结构体或者希望保持不可变性的情况。

```go
type MyStruct struct {
    Field int
}

// 值接收者
func (s MyStruct) ValueReceiverMethod() {
    s.Field = 10
}
```

### 2、指针接收者

指针接收者允许直接修改接收者指向的原始对象。这种方式在方法调用时不会创建接收者的

### 3、两者区别

| 特性   | 值接收者                      | 指针接收者                 |
| ---- | ------------------------- | --------------------- |
| 关联对象 | 与特定类型（如结构体、类型别名）关联。       | 独立于类型，可以在包的任何地方定义。    |
| 接受者  | 有接收者，表示方法属于并可访问该类型的实例或指针。 | 没有接收者，仅定义参数列表和返回值。    |
| 调用方式 | 通过类型的实例或者指针调用。            | 直接使用函数名和参数列表调用。       |
| 定义位置 | 必须在一个具体的类型定义内部。           | 可以在包的任何位置定义，不依赖于特定类型。 |