---
title: "Go 语言中如何查看正在执行 goroutine 的数量"
date: 2024-03-25T17:54:10+08:00
draft: false
description: "Go 语言中如何查看正在执行 goroutine 的数量。"

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

## Go 语言中如何查看正在执行 goroutine 的数量

### 1、程序中引入 pprof package

```go
import _ "net/http/pprof"
```

### 2、程序中开启 HTTP 监听服务

```go
package main

import (
	"net/http"
	_ "net/http/pprof"
)

func main() {
	for i := 0; i < 10; i++ {
		go func() {
			select {}
		}()
	}
	go func() {
		err := http.ListenAndServe("127.0.0.1:6060", nil)
		if err != nil {
			panic(err)
		}
	}()

	select {}
}
```

### 3、分析 goroutine 文件

shell 执行如下命令

```shell
go tool pprof -http=:1248 http://127.0.0.1:6060/debug/pprof/goroutine
```

会自动打开浏览器页面如下图所示

![golang-pprof-result](https://file.yingnan.wang/golang/golang-pprof-result.png)

在图中可以清晰的看到 goroutine 的数量以及调用关系，可以看到有 103 个 goroutine。