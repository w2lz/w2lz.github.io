---
title: "Go 语言中 goroutine 泄露的场景"
date: 2024-03-25T17:36:51+08:00
draft: false
description: "Go 语言中 goroutine 泄露的场景。"

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

## Go 语言中 goroutine 泄露的场景

### 1、什么是 goroutine 泄露

### 2、泄露场景

#### 1、nil channel

#### 2、channel 发送未接收

#### 3、channel 接收未发送

#### 4、资源连接未关闭

#### 5、互斥锁忘记解锁

#### 6、sync.WaitGroup 使用不当

#### 7、无限循环

### 3、如何排查