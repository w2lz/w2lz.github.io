---
title: "Go 语言中 channel 发送和接收什么情况下会死锁"
date: 2024-03-20T14:50:01+08:00
draft: false
description: "Go 语言中 channel 发送和接收什么情况下会死锁。"

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

## Go 语言中 channel 发送和接收什么情况下会死锁

### 1、什么是死锁

### 2、channel 死锁场景

#### 1、无缓冲 channel 只写不读

#### 2、无缓冲 channel 读在写后面

#### 3、缓存 channel 写入超过缓冲区数量

#### 4、空读

#### 5、多个协程互相等待