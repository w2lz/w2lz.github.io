---
title: "Go 语言中 map 的负载因子为什么是6.5"
date: 2024-03-11T18:07:10+08:00
draft: false
description: "Go 语言中 map 的负载因子为什么是6.5。"

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

## Go 语言中 map 的负载因子为什么是6.5

在 Go 语言中，map 的负载因子（load factor）是指哈希表中已经填充的位置占总位置的比例。当这个比例超过一定阈值的时候，map 会进行扩容，以保持操作的效率。Go 语言选择 6.5 作为负载因子的阈值是出于性能和内存使用的平衡考虑。

### 1、什么是负载因子

负载因子（load factor），用语衡量当前哈希表中空间占用率的核心指标，就是平均每个 bucket 存储的元素个数。

计算公式如下：LoadFactor（负载因子）= hash 表中已存储的键值对的总数量 / hash 桶的个数（即 hmap 结构中的 buckets 数组的长度）。

在 GO 语言中，map 的负载因子（load factor）默认值为 6.5，当负载因子达到 6.5 的时候，Go 会自动出发扩容操作，将哈希表容量进行翻倍，并重新分配元素，以保持性能和内存的平衡。这种自动管理的方式使得使用 map 变得非常方便，同时也可以获得不错的性能。

### 2、为什么是 6.5

为什么 Go 语言中哈希表的负载因子是 6.5，为什么不是 8，也不是 1.这里面有可靠的数据支持吗？实际上这是 Go 官方经过认真的测试得出的数字，下面是测试报告，报告中共包含 4 个关键指标，如下：

![golang-map-loadfactor](https://file.yingnan.wang/golang/golang-map-loadfactor.png)

loadFactor：负载因子，也有叫装载因子。

overflow：溢出率，有溢出 bucket 的百分比。

bytes/entry：平均每对 key/value 的开销字节数。

hitprobe：查找一个存在的 key 时，要查找的平均个数。

missprpbe：查找一个不存在的 key 的时候，要查找的平均个数。

为什么选择 6.5 作为默认负载因子呢？这是一种权衡的结果，考虑了性能和内存的因此：

1. 减少哈希冲突：当负载因子过高时，桶的数量相对少，会出现多个键值被映射到同一个哈希桶的情况，导致冲突的概率增加，这会降低访问元素的速度。

2. 减少内存浪费：当负载因子过低时，桶的数量相对多，导致有许多空的或未充分利用的桶，意味着内存利用率不高。

3. 重新哈希成本：哈希表的扩容操作会引入一定的成本，因为它涉及到重新计算哈希值、重新分配桶等操作。过于频繁的扩容会影响性能，因此选择一个适度的负载因子可以减少扩容的次数。

6.5 是一个比较理想的值，可以在减少哈希冲突的同时，保证了哈希表的空间利用率。