---
title: "Go 语言中 channel 有什么特点"
date: 2024-03-20T14:46:49+08:00
draft: false
description: "Go 语言中 channel 有什么特点。"

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

## Go 语言中 channel 有什么特点

### 1、channel 的种类

channel 有 2 种类型：无缓冲、有缓冲。

### 2、channel 的模式

channel 有 3 种模式：写操作模式（单向通道）、读操作模式（单向通道）、读写操作模式（双向通道）。

|     | 写操作模式            | 读操作模式            | 读写操作模式         |
| --- | ---------------- | ---------------- | -------------- |
| 创建  | make(chan<- int) | make(<-chan int) | make(chan int) |

### 3、channel 的状态

channel 有 3 种状态：未初始化、正常、关闭

|     | 未初始化     | 关闭                 | 正常        |
| --- | -------- | ------------------ | --------- |
| 关闭  | panic    | panic              | 正常关闭      |
| 发送  | 永远阻塞导致死锁 | panic              | 阻塞或者成功发送。 |
| 接收  | 永远阻塞导致死锁 | 缓冲区为空则为零值，否则可以继续读。 | 阻塞或者成功接收。 |

### 4、注意点

一个 channel 不能多次关闭，会导致 panic。如果多个 goroutine 都监听同一个 channel，那么 channel 上的数据就可能随机被某一个 goroutine取走进行消费。

如果多个 goroutine 监听同一个 channel，如果这个 channel 被关闭，则所有 goroutine 都能接受到退出信号。