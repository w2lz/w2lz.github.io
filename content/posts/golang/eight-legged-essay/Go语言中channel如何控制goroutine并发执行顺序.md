---
title: "Go 语言中 channel 如何控制 goroutine 并发执行顺序"
date: 2024-03-20T14:48:33+08:00
draft: false
description: "Go 语言中 channel 如何控制 goroutine 并发执行顺序。"

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

## Go 语言中 channel 如何控制 goroutine 并发执行顺序

多个 goroutine 并发执行时，每一个 goroutine 抢到处理器的时间不一致，goroutine 的执行本身不能保证顺序，即代码中先写的 goroutine 并不能保证先执行。

思路：使用 channel 进行通信通知，用 channel 去传递消息，从而控制并发顺序。

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

var wg sync.WaitGroup

func main() {
	ch1 := make(chan struct{}, 1)
	ch2 := make(chan struct{}, 1)
	ch3 := make(chan struct{}, 1)
	ch1 <- struct{}{}
	wg.Add(3)
	start := time.Now().Unix()
	go print("goroutine1", ch1, ch2)
	go print("goroutine2", ch2, ch3)
	go print("goroutine3", ch3, ch1)
	wg.Wait()
	end := time.Now().Unix()
	fmt.Printf("duration: %d\n", end-start)
}

func print(goroutine string, inputchan chan struct{}, outchan chan struct{}) {
	// 模拟内部操作耗时
	time.Sleep(1 * time.Second)
	select {
	case <-inputchan:
		fmt.Printf("%s\n", goroutine)
		outchan <- struct{}{}
	}
	wg.Done()
}
```

输出：

```shell
goroutine1
goroutine2
goroutine3
duration: 1
```