---
title: "Go 语言中 map 如何查找"
date: 2024-03-11T18:06:02+08:00
draft: false
description: "Go 语言中 map 如何查找。"

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

## Go 语言中 map 如何查找

### 查找流程

#### 1、写保护监测

#### 2、计算 hash 值

#### 3、找到 hash 对应的 bucket

#### 4、遍历 bucket 查找

#### 5、返回 key 对应的指针