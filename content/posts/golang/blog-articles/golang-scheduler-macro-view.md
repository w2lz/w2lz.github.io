---
title: "2.Go调度器系列-宏观看调度器"
date: 2023-03-01T15:00:18+08:00
draft: false
description: "从宏观的三个角度，去看待和理解调度器，对调度器有个宏观的认识。"

tags:
  - "Go调度器系列"
  - "GMP"
categories:
  - "Golang"

featuredImage: "/posts/golang/blog-articles/images/golang-logo.jpeg"
featuredImagePreview: "/posts/golang/blog-articles/images/golang-logo.jpeg"

toc:
  enable: true
  auto: true
---

<!--more-->

三个角度分别是：

1. 调度器的宏观组成

2. 调度器的生命周期

3. GMP的可视化感受

调度器相关的三个缩写：

- G：goroutine，每个G都代表1个goroutine

- M：工作线程，是Go语言定义出来在用户层面描述系统现成的对象，每个M代表一个系统线程。运行在操作系统的核心态，在Go中支持最大的M的数量是10000，但是操作系统中通常情况是不可以创建这么多的线程的。

- P：处理器，它包含了运行Go代码的资源。可以理解成一个等待分发给M调度执行的 goroutine队列。P的个数是由runtime的GOMAXPROCS来决定的。

- 三者的简单关系是P拥有G，M必须和一个P关联才能运行P拥有的G。

## 调度器的功能

协程和线程的关系是，协程需要运行在线程之上，线程由CPU进行调度。在Go中，线程是运行goroutine的实体，调度器的功能是把可运行的goroutine分配到工作线程上。

Go的调度器也是经过了多个版本的迭代才是现在的样子的：

- 1.0版本发布了最初的、最简单的调度器，是G-M模型，存在4类问题。

- 1.1版本重新设计，修改为G-M-P模型，奠定了当前调度器的基本模样。

- 1.2版本加入了抢占式调度，防止协程不让出CPU导致其他G饿死。

## Scheduler的宏观组成

从宏观的角度展示调度器的重要组成，需要将goroutine调度器和系统调度器两者相结合，不能把二者割裂开。

![goroutine-scheduler-model](https://file.yingnan.wang/golang/goroutine-scheduler-model.png)

如图所示，是调度器的4个组成部分：

1. 全局队列（Global Queue）：存放等待运行的G。

2. P的本地队列：同全局队列类似，存放的也是等待运行的G，存放的数量有限，不超过256个。新建G’时，G’优先加入到P的本地队列，如果本地队列满了，则会把本地队列中一半的G移动到全局队列。

3. P列表，所有的P都在程序启动时创建，并保存在数组中，最多有GOMAXPROCS个。

4. M：线程像运行任务就得获取P，从P的本地队列获取G，P队列为空时，M也会尝试从全局队列拿一批G放到本地队列，或从其他P的本地队列偷一半放到自己P的本地队列。M运行G，G执行之后，M会从P获取下一个G，不断重复下去。

Goroutine调度器和OS调度器是通过M结合起来的，每个M都代表了一个内核线程，OS调度器负责把内核线程分配到CPU的核上去执行。

## goroutine之旅

通过一幅图，展示一下我们在代码中执行go func(){}后，GMP模型是如何工作的：

![goroutine-journey](https://file.yingnan.wang/golang/goroutine-journey.png)

1. 首先创建一个新的goroutine。

2. 如果本地的局部队列中有足够的空间可以存放，则放入局部队列中；如果局部队列满，则放入一个全局队列（所有的M都可以从全局队列中拉取G来执行）。

3. 所有的G都必须在M上才可以被执行，M和P存在一一绑定的关系，如果M绑定的P中存在可以被执行的G，则从P中拉取G来执行；如果P中为空，没有可执行的G，则M从全局队列中拉取；如果全局队列也为空，则从其他的P中拉取G。

4. 为G的运行分配必要的资源，等待CPU的调度。

5. 分配到CPU，执行func(){}。

## 调度器的生命周期

所有的Go程序运行都会经过一个完整的调度器生命周期：从创建到结束。

![scheduler-lifetime](https://file.yingnan.wang/golang/scheduler-lifetime.png)

附上一段简单的代码：

```go
package main

import "fmt"


func main() {
    fmt.Println("Hello World")
}
```

上述代码会经历如上图所示的过程：

1. runtime创建最初的线程m0和goroutine g0，并把二者关联。

2. 调度器初始化：初始化m0，栈、垃圾回收，以及创建和初始化由GOMAXPROCS个P构成的P列表。

3. 示例代码中的main函数是main.main，runtime也有一个main函数--runtime.main，代码经过编译后，runtime.main会调用main.main，程序启动时会为runtime.main创建goroutine，称它为main goroutine吧，然后把main goroutine加入到P的本地队列。

4. 启动m0，m0已经绑定了P，会从P的本地队列获取G，获取到main goroutine。

5. G拥有栈，M会根据G中的栈信息和调度信息设置运行环境。

6. M运行G。

7. G退出，再次回到M获取可运行的G，这样重复下去，直到main.main退出，runtime.main执行defer和panic处理，或调用runtime.exit退出程序。

调度器的生命周期几乎占满了一个Go程序的一生，runtime.main的goroutine执行之前都是为调度器做准备工作，runtime.main的goroutine运行，才是调度器真正开始，直到runtime.main结束而结束。

## GMP的可视化感受

有两种方式可以从可视化角度感受到调度器：

**方式1：go tool trace**

使用命令go tool trace trace.out生成页面，可以通过这个方式查看goroutine（G）、堆、线程（M）、Proc（P）的信息。

**方式2：Debug trace**

使用go build . 和GODEBUG=schedtrace=1000 ./one_routine2，可以在控制台看到一些信息。