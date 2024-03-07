---
title: "Go语言中slice深拷贝和浅拷贝"
date: 2024-03-07T17:50:49+08:00
draft: false
description: "Go语言中 slice 深拷贝和浅拷贝。"

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

## Go语言中 slice 深拷贝和浅拷贝

### 1、深拷贝

拷贝的是数据本身，创造一个新对象，新创建的对象与原对象不共享内存，新创建的对象在内存中开辟一个新的内存地址，新对象值修改时不会影响原对象的值。实现深拷贝的方式有两种。

#### copy(slice2, slice1)

```go
copy(slice2, slice1)
```

#### 遍历 append 赋值

```go
package main

import "fmt"

func main() {
	slice1 := []int{1, 2, 3, 4, 5}
	slice2 := make([]int, 5, 5)
	fmt.Printf("slice1: %v, %p\n", slice1, slice1)
	copy(slice2, slice1)
	fmt.Printf("slice2: %v, %p\n", slice2, slice2)
	slice3 := make([]int, 0, 5)
	for _, v := range slice1 {
		slice3 = append(slice3, v)
	}
	fmt.Printf("slice3: %v, %p\n", slice3, slice3)
}

slice1: [1 2 3 4 5], 0xc00012e690
slice2: [1 2 3 4 5], 0xc00012e6c0
slice3: [1 2 3 4 5], 0xc00012e6f0
```

### 2、浅拷贝

浅拷贝的是数据地址，只复制指向的对象的指针，此时新对象和老对象指向的内存地址是一样的，新对象值修改时老对象也会变化。

实现浅拷贝的方式：默认复制。引用类型的变量，默认赋值操作就是浅拷贝：slice2 := slice1

```go
package main

import "fmt"

func main() {
	slice1 := []int{1, 2, 3, 4, 5}
	fmt.Printf("slice1: %v, %p\n", slice1, slice1)
	slice2 := slice1
	fmt.Printf("slice2: %v, %p\n", slice2, slice2)
}

slice1: [1 2 3 4 5], 0xc000114690
slice2: [1 2 3 4 5], 0xc000114690
```