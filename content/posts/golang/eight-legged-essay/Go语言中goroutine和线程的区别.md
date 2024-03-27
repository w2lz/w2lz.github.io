---
title: "Go 语言中 goroutine 和线程的区别"
date: 2024-03-25T17:36:30+08:00
draft: false
description: "Go 语言中 goroutine 和线程的区别。"

tags:
  - "Go协程面试问题合集"
categories:
  - "Go面试"

featuredImage: "/posts/golang/eight-legged-essay/images/golang-ms.png"
featuredImagePreview: "/posts/golang/eight-legged-essay/images/golang-ms.png"

toc:
  enable: true
  auto: true
---

<!--more-->

## Go 语言中 goroutine 和线程的区别

| 特性    | goroutine                                         | 线程                                              |
| ----- | ------------------------------------------------- | ----------------------------------------------- |
| 内存占用  | 创建一个 goroutine 的内存消耗为 2KB                         | 创建一个线程的栈的默认消耗为 1MB                              |
| 创建和销毁 | goroutine 因为是由 Go runtime 负责管理的，创建和销毁的消耗非常小，是用户级。 | 线程创建和销毁都会有巨大的消耗，因为要和操作系统打交道，是内核级的，通常解决的办法就是线程池。 |
| 切换操作  | goroutine 切换只需保存和恢复寄存器：PC、SP、BP                   | 当线程切换时，需要保存各种寄存器以便恢复现场。                         |
| 切换耗时  | goroutine 切换约为 200ns，相当于 2400-3600 条指令。           | 线程切换会消耗为 1.5-2.0μs，相当于 12000-18000 条指令。         |