---
title: "Go 语言中 channel 共享内存有什么优劣势"
date: 2024-03-20T14:49:16+08:00
draft: false
description: "Go 语言中 channel 共享内存有什么优劣势。"

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

## Go 语言中 channel 共享内存有什么优劣势

"不要通过共享内存来通信，应该使用通信来共享内存"，这句话大家在官方的博客、初学时的教程甚至在 Go 语言的源码中肯定都看到过。

无论是通过共享内存来通信还是通过通信来共享内存，最终我们应用程序都是读取的内存当中的数据，只是前者是直接读取内存的数据，而后者是通过发送消息的方式来进行同步。

而通过发送消息来同步的这种方式常见的就是 Go 采用的 CPS（Communication Sequential Process）模型以及 Erlang 采用的 Actor 模型，这两种方式都是通过通信来共享内存的。

![csp-actor](https://file.yingnan.wang/golang/csp-actor.png)

大部分的语言采用的都是第一种方式直接去操作内存，然后通过互斥锁，CAS 等操作来保证并发安全。Go 引入了 Channel 和 Goroutine 实现了 CSP 模型将生产者和消费者进行了解耦，Channel 其实和消息队列很相似。

而 Actor 模型和 CSP 模型都是通过发送消息来共享内存的，但是它们之间最大的区别就是 Actor 模型当中并没有一个独立的 Channel 组件，而是 Actor 与 Actor 之间直接进行消息的发送与接收，每个 Actor 都有一个本地的"信箱"，消息都会先发送到这个"信箱当中"。

在 Go 语言种，使用 Channel 进行 goroutines 间的通信是一种遵循"不通过共享内存来通信；而是通过通信来共享内存"的利剑。这种方法与传统的基于共享内存的并发模型相比，具有其独特的优势和劣势。

### 1、优势

#### 1、解耦

使用 channel 可以帮助我们解耦生产者和消费者，可以降低并发当中的耦合。

#### 2、清晰的并发结构

Channel 使得并发逻辑更加明确和直观，因为数据交换和同步都通过明确的通信进行，有助于编写可读性高和易于理解的并发代码。

#### 3、灵活的通信机制

Channel 可用于同步、消息传递、信号通知等多种场景，他们提供了阻塞和非阻塞操作，以及配合 select 语句进行多路复用。

### 2、劣势

#### 1、性能开销

相比直接访问共享内存，通过 Channel 通信可能引入额外的性能开销，Channel 的操作设计锁和 goroutine 之间的调度，这可能比直接访问共享变量更耗时。

#### 2、死锁

在一些场景中，使用 Channel 可能导致设计更为复杂，特别是在需要处理多个通道和复杂的同步逻辑时，不当的使用还可能导致死锁。

#### 3、学习曲线

对于习惯了传统共享内存并发的开发者来说，理解和掌握基于 Channel 的并发模型可能需要一定时间。

总体来说，Channel 在 Go 语言种提供了一种强大且高级的方式来处理并发和 goroutine 间的通信，它带来了代码的清晰性和安全性，但也可能引入性能开销和设计复杂性。