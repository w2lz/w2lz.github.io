---
title: "Go语言中slice扩容机制"
date: 2024-03-07T17:51:06+08:00
draft: false
description: "Go 语言中 slice 扩容机制。"

tags:
  - "Go切片面试问题合集"
categories:
  - "Go面试"

featuredImage: "/posts/golang/eight-legged-essay/images/golang-ms.png"
featuredImagePreview: "/posts/golang/eight-legged-essay/images/golang-ms.png"

toc:
  enable: true
  auto: true
---

<!--more-->

## Go 语言中 slice 扩容机制

扩容会发生在 slice append 的时候，当 slice 的 cap 不足以容纳新元素，就会进行扩容，扩容的规则如下：

1. 如果新申请容量比两倍原有容量大，那么扩容后容量大小为新申请容量。

2. 如果原有 slice 长度小于 1024，那么每次就扩容为原来的 2 倍。

3. 如果原slice 长度大于等于 1024，那么每次扩容就扩为原来的 1.25 倍。

```go
package main

import "fmt"

func main() {
	slice1 := []int{1, 2, 3}
	for i := 0; i < 16; i++ {
		slice1 = append(slice1, i)
		fmt.Printf("addr: %p, len: %v, cap: %v\n", slice1, len(slice1), cap(slice1))
	}
}
```

输出：

```shell
addr: 0xc00001e750, len: 4, cap: 6
addr: 0xc00001e750, len: 5, cap: 6
addr: 0xc00001e750, len: 6, cap: 6
addr: 0xc00006e480, len: 7, cap: 12
addr: 0xc00006e480, len: 8, cap: 12
addr: 0xc00006e480, len: 9, cap: 12
addr: 0xc00006e480, len: 10, cap: 12
addr: 0xc00006e480, len: 11, cap: 12
addr: 0xc00006e480, len: 12, cap: 12
addr: 0xc000274000, len: 13, cap: 24
addr: 0xc000274000, len: 14, cap: 24
addr: 0xc000274000, len: 15, cap: 24
addr: 0xc000274000, len: 16, cap: 24
addr: 0xc000274000, len: 17, cap: 24
addr: 0xc000274000, len: 18, cap: 24
addr: 0xc000274000, len: 19, cap: 24
```