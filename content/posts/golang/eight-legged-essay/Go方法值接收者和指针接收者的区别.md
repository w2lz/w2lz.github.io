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

当方法使用值接收者时，它在每次方法调用时都会获取接收者的一个副本。

这意味着方法对接收者的任何修改都不会影响原始对象。

值接收者适用于小型结构体或者希望保持不可变性的情况。

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

指针接收者允许直接修改接收者指向的原始对象。

这种方式在方法调用时不会创建接收者的副本，因此对于大型结构体或需要修改状态的场景更有效率。

使用指针接收者可以避免每次调用时的内存拷贝，提高性能。

```go
type MyStruct struct {
    Field int
}

func (s *MyStruct) PointerReceiverMethod() {
    s.Field = 10
}
```

### 3、两者区别

| 特性   | 值接收者                       | 指针接收者                 |
| ---- | -------------------------- | --------------------- |
| 内存副本 | 创建接收者的副本，原始对象不受影响。         | 直接使用原始对象的指针，无副本创建。    |
| 修改对象 | 不能修改原始对象。方法内的修改只影响副本。      | 可以修改原始对象。             |
| 适用场景 | 适用于小型结构体或不需要修改对象的情况。       | 适用于大型结构体或需要修改对象状态的情况。 |
| 性能   | 对于小对象，性能较好；大对象可能导致较大的内存开销。 | 避免了内存拷贝，对于大对象性能较好。    |