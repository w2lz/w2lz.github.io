---
title: "Go 语言中 map 遍历为什么是无序的"
date: 2024-03-11T18:05:13+08:00
draft: false
description: "Go 语言中 map 遍历为什么是无序的。"

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

## Go 语言中 map 遍历为什么是无序的

使用 range 多次遍历 map 的时候输出的 key 和 value 的顺序可能不同，这是 Go 语言的设计者有意为之，为的是提示开发者们，Go 底层实现并不保证 map 遍历顺序稳定，请大家不要依赖 range 遍历结果顺序。

### 1、主要原因如下

map 在遍历时，并不是从固定的 0 号 bucket 开始遍历的，每次遍历，都会从一个随机值序号的 bucket，再从其中随机的 cell 开始遍历。

map 遍历时，是按序遍历 bucket，同时按序遍历 bucket 中和其 overfow bucket 中的 cell。但是 map 在扩容后，会发生 key 的搬迁，这造成了原来落在一个 bucket 中的 key，搬迁后，有可能会落到其他 bucket 中了，从这个角度看，遍历 map 的结果就不可能是按照原来的顺序了。

### 2、如何有序遍历 map

map 本身是无序的，且遍历时顺序还会被随机化，如果想顺序遍历 map，需要对 map key 选排序，再按照 key 的顺序遍历 map。

```go
package main

import (
    "sort"
    "testing"
)

func TestMapRange(t *testing.T) {
    m := map[int]string{1: "a", 2: "b", 3: "c"}
    t.Log("first range")
    for i, v := range m {
        t.Logf("m[%v]=%v", i, v)
    }
    t.Log("\nsecond range")
    for i, v := range m {
        t.Logf("m[%v]=%v", i, v)
    }

    // 实现有序遍历
    var sl []int
    // 把 key 单独取出放到切片
    for k := range m {
        sl = append(sl, k)
    }

    // 排序切片
    sort.Ints(sl)

    // 以切片中的 key 顺序遍历 map 就是有序的
    for _, k := range sl {
        t.Logf("m[%v]=%v", k, m[k])
    }
}

=== RUN   TestMapRange
    main_test.go:10: first range
    main_test.go:12: m[1]=a
    main_test.go:12: m[2]=b
    main_test.go:12: m[3]=c
    main_test.go:14: 
        second range
    main_test.go:16: m[3]=c
    main_test.go:16: m[1]=a
    main_test.go:16: m[2]=b
    main_test.go:31: m[1]=a
    main_test.go:31: m[2]=b
    main_test.go:31: m[3]=c
--- PASS: TestMapRange (0.00s)
PASS
```