---
title: "Go 语言中 map 冲突的解决方式"
date: 2024-03-11T18:06:24+08:00
draft: false
description: "Go 语言中 map 冲突的解决方式。"

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

## Go 语言中 map 冲突的解决方式

在发生哈希冲突时，Python 中 dict 采用的开放寻址法，Java 中的 HashMap 采用的是链地址法，而 Go map 也采用链地址法解决冲突，具体就是插入 key 到 map 中时，当 key 定位的桶填满 8 个元素后（这里的单元就是桶，不是元素），将会创建一个溢出桶，并且将溢出桶插入当前桶所在链表尾部。

```go
if inserti == nil {
    // all current buckets are full, allocate a new one
    newb := h.newoverflow(t, b)
    inserti = &newb.tophash[0]
    insertk = add(unsafe.Pointer(newb), dataOffset)
    elem = add(insertk, bucketCnt*uintptr(t.keysize))
}
```

比较常用的 Hash 解决冲突方案有链地址法和开放寻址法：

### 1、链地址法

当哈希冲突发生时，创建新单元，并将新单元添加到冲突单元所在的链表尾部。

### 2、开放寻址法

当哈希冲突发生时，从发生冲突的那个单元起，按照一定的次序，从哈希表中寻找一个空闲的单元，然后把发生冲突的元素存入到该单元。开放寻址法需要的表长度要大于等于所需要存放的元素数量。

开放寻址法有多种方式：线性探测法、平方探测法、随机探测法和双重哈希法。这里以线性探测法举例。

#### 1、线性探测法

设 Hash(key) 表示关键词 key 的哈希值，表示哈希表的槽位数（哈希表的大小）。

线性探测法则可以表示为：

- 如果 Hash(x) % M 已经有数据，则尝试(Hash(x) + 1) % M；

- 如果(Hash(x) + 1) % M 也有数据了，则尝试(Hash(x) + 2) %M；

- 如果(Hash(x) + 2) % M 也有数据了，则尝试(Hash(x) + 3) % M；

### 3、两种解决方案比较

对于链地址法，基于数组+链表进行存储，链表节点可以在需要时再创建，不必像开放寻址法那样事先申请足够内存，因此链地址法对于内存的利用率会比开放寻址法高。链地址法对于装载因子的容忍度会更高，并且适合存储大对象、大数据量的哈希表。而且相较于开放寻址法，它更加灵活，支持更多的优化策略，比如可采用红黑树代替链表。但是链地址法需要二外的空间来存储指针。

对于开放寻址法，它只有数组一种数据结构就可完成存储，继承了数组的有点，对 CPU 缓存有好，易于序列化操作。但是它对内存的利用率不如链地址法，且发生冲突时代价更高。当数据量明确、装载因子小，适合采用开放寻址法。