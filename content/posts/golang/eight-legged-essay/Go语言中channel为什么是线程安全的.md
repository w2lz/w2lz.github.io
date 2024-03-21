---
title: "Go 语言中 channel 为什么是线程安全的"
date: 2024-03-20T14:47:28+08:00
draft: false
description: "Go 语言中 channel 为什么是线程安全的。"

tags:
  - "Go管道面试问题合集"
categories:
  - "Go面试"

featuredImage: "/posts/golang/eight-legged-essay/images/golang-ms.png"
featuredImagePreview: "/posts/golang/eight-legged-essay/images/golang-ms.png"

toc:
  enable: true
  auto: true
---

<!--more-->

## Go 语言中 channel 为什么是线程安全的

### 1、为什么设计成线程安全

不同协程通过 channel 进行通信，本身的使用场景就是多线程，为了保证数据的一致性，必须实现线程安全。

### 2、如何实现线程安全的

channel 的底层实现种，hchan 结构体种采用 Mutex 锁来保证数据读写安全。在对循环数组 buf 中的数据进行入队和出队操作时，必须先获取互斥锁，才能操作 channel 数据。