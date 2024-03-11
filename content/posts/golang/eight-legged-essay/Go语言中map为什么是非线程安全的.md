---
title: "Go 语言中 map 为什么是非线程安全的"
date: 2024-03-11T18:05:44+08:00
draft: false
description: "Go 语言中 map 为什么是非线程安全的。"

tags:
  - "Go哈希表面试问题合集"
categories:
  - "Go面试"

featuredImage: "/posts/golang/eight-legged-essay/images/golang-ms.png"
featuredImagePreview: "/posts/golang/eight-legged-essay/images/golang-ms.png"

toc:
  enable: true
  auto: true
---

<!--more-->

## Go 语言中 map 为什么是非线程安全的

### 1、使用读写锁 map + sync.RWMutex

### 2、使用 Go 提供的 sync.Map