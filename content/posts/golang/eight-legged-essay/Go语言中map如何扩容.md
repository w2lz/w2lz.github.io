---
title: "Go 语言中 map 如何扩容"
date: 2024-03-11T18:07:42+08:00
draft: false
description: "Go 语言中 map 如何扩容。"

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

## Go 语言中 map 如何扩容

### 1、扩容时机

### 2、扩容条件

#### 1、超过负载

#### 2、溢出桶太多

### 3、扩容机制

#### 1、双倍扩容

#### 2、等量扩容

#### 3、扩容函数