---
title: "3.Go调度器系列-图解调度原理"
date: 2023-03-02T14:14:54+08:00
draft: false
description: "通过12个主要场景介绍Go调度器的基本原理。"

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

本文的12个场景主要覆盖以下内容：

1. G的创建和分配。

2. P的本地队列和全局队列的负载均衡。

3. M如何寻找G。

4. M如何从G1切换到G2。

5. work stealing，M如何去偷取G。

6. 为何需要自旋线程。

7. G进行系统调用，如何保证P的其他G’可以被执行，而不是饿死。

8. Go调度器de抢占。

## 12个场景

> 场景描述在前，图在后；图中三角形、正方形、圆形分别代表了M、P、G，正方形连接的绿色长方形代表了P的本地队列，蓝色长方形代表全局队列。

### 场景1：

场景描述：p1拥有g1，m1获取p1后开始运行g1，g1使用go func()创建了g2，为了局部性g2优先加入到p1的本地队列。

![golang-scheduler-scene-1](https://file.yingnan.wang/golang/golang-scheduler-scene-1.png)

### 场景2：

场景描述：g1运行完成后（函数：goexit），m上运行的goroutine切换为g0，g0负责调度时切成的切换（函数：schedule）。从p1的本地队列取g2，从g0切换到g2，并开始运行g2（函数：execute）。实现了线程m1的复用。

![golang-scheduler-scene-2](https://file.yingnan.wang/golang/golang-scheduler-scene-2.png)

### 场景3：

场景描述：假设每个p的本地队列只能存4个g，g2要创建6个g，前4个g（g3、g4、g5、g6）已经加入到p1的本地队列，p1的本地队列满了。

![golang-scheduler-scene-3](https://file.yingnan.wang/golang/golang-scheduler-scene-3.png)

### 场景4：

场景描述：g2在创建g7的时候，发现p1的本地队列已满，需要执行负载均衡，把p1中本地队列中前一半的g，还有新创建的g转移到全局队列（实现中并不一定是新的g，如果g是g2之后就创建的，会被保存到本地队列，利用某个老的g替换新g加入全局队列），这些g被转移到全局队列时，会被打乱顺序。所以g3、g4、g7被转移到全局队列。

![golang-scheduler-scene-4](https://file.yingnan.wang/golang/golang-scheduler-scene-4.png)

### 场景5：

场景描述：g2创建g8的时候，p1的本地队列未满，所以g8会被加入到p1的本地队列。

![golang-scheduler-scene-5](https://file.yingnan.wang/golang/golang-scheduler-scene-5.png)

### 场景6：

场景描述：在创建g时，运行的g会尝试唤醒其他空闲的p和m执行。假定g2唤醒了m2，m2绑定了p2，并运行g0，但p2的本地队列没有g，m2此时为自旋线程（没有G但为运行状态的线程，不断寻找g）

![golang-scheduler-scene-6](https://file.yingnan.wang/golang/golang-scheduler-scene-6.png)

### 场景7：

场景描述：m2尝试从全局队列（GQ）中取一批g放到p2的本地队列（函数：findrunnable）。m2从全局队列取的g数量符合下面的公式：

n = min(len(GQ) / GOMAXPROCS + 1, len(GQ / 2))

公式的含义是，至少从全局队列中取1个g，但每次不要从全局队列移动太多的g到p本地队列，给其他p留一些。这是从全局队列到P本地队列的负载均衡。

假定我们这个场景一共有4个P，所以m2只能从全局队列取1个g（即g3），移动p2本地队列，然后完成从g0到g3的切换，运行g3。

![golang-scheduler-scene-7](https://file.yingnan.wang/golang/golang-scheduler-scene-7.png)

### 场景8：

场景描述：假设g2一直在m1上运行，经过两轮后，m2已经把g7、g4也挪到了p2的本地队列并完成运行，全局队列和p2的本地队列都空了，如下图左边。

全局队列已经没有g，那么m就要执行work stealing：从其他有g的p那里偷一半g过来，放到自己的本地队列。p2从p1的本地队列尾部取一半的g，本例中一半则只有1个g（即g8），放到p2的本地队列，情况如下图右边。

![golang-scheduler-scene-8](https://file.yingnan.wang/golang/golang-scheduler-scene-8.png)

### 场景9：

场景描述：p1本地队列g5、g6已经被其他m偷走并且运行完成，当前m1和m2分别在运行g2和g8，m3和m4没有goroutine可以运行，m3和m4就处于自旋状态，他们不断寻找goroutine。为什么要让m3和m4自旋，自旋的本质是运行，现成在运行却没有执行g，就变成了浪费CPU？销毁线程不是更好吗？可以节约CPU资源。创建和销毁CPU都是浪费时间的，我们希望当有新的goroutine创建时，立刻能有m运行它，如果销毁再新建就增加了时延，降低了效率。当然也考虑了过多的自旋线程是浪费CPU，所以系统重最多有GOMAXPROCS个自旋的线程，多余的没事做线程会让它们休眠（函数：notesleep()）。

![golang-scheduler-scene-9](https://file.yingnan.wang/golang/golang-scheduler-scene-9.png)

### 场景10：

场景描述：假定当前除了m3和m4为自旋线程，还有m5和m6为自旋线程，g8创建了g9，g8进行了阻塞的系统调用，m2和p2立即解绑，p2会执行以下判断：如果p2本地队列有g、全局队列有g或者有空闲的m，p2都会立马唤醒1个m和它绑定，否则p2则会加入到空闲P列表，等待m来获取可用的p。本场景中，p2本地队列有g，可以和其他自旋线程m5绑定。

![golang-scheduler-scene-10](https://file.yingnan.wang/golang/golang-scheduler-scene-10.png)

### 场景11：

场景描述：g8创建了g9，假定g8进行了非阻塞系统调用（CGO会是这种方式，见cgocall()），m2和p2会解绑，但是m2会记住p，然后g8和m2进入系统调用状态。当g8和m2退出系统调用时，会尝试获取p2，如果无法获取，则获取空闲的p，如果依然没有，g8会被记为可运行状态，并被加入到全局队列。

### 场景12：

场景描述：Go调度在go1.12实现了抢占，应该更精确的成为请求式抢占，那是因为go调度器的抢占和OS的线程抢占比起来很柔和，不暴力，不会说线程时间片到了，或者更高优先级的任务到了，执行抢占调度。go的抢占调度柔和到只给goroutine发送一个抢占请求，至于goroutine何时停下来，那就管不到了。抢占请求需要满足两个条件的中的一个：

1. G进行系统调用超过20us。

2. G运行超过10ms。

调度器在启动的时候会启动一个单独的线程sysmon，它负责所有的线程工作，其中1项就是抢占，发现满足抢占条件的G时，就发出抢占请求。

## 场景融合

把上面所有的场景都融合起来，构成下面的图，它从整体的角度描述了Go调度器各部分的关系。图的上半部分是G的创建、负载均衡和work stealing，下半部分是M不停寻找和执行G的迭代过程。

![scene-fuse](https://file.yingnan.wang/golang/scene-fuse.png)

总结一下，Go调度器和OS调度器相比，是相当的轻量和简单，但它已经足以撑起goroutine的调度工作了，并且让Go具有了原生并发的能力。Go调度本质是把大量的goroutine分配到少量线程上去执行，并利用多核并行，时间更强大的并发。