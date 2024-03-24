---
title: "Go 函数参数传递到底是值传递还是引用传递"
date: 2024-03-05T13:48:47+08:00
draft: false
description: "Go 函数参数传递到底是值传递还是引用传递？"

tags:
  - "Go基础面试问题合集"
categories:
  - "Go面试"

featuredImage: "/posts/golang/eight-legged-essay/images/golang-ms.png"
featuredImagePreview: "/posts/golang/eight-legged-essay/images/golang-ms.png"

toc:
  enable: true
  auto: true
---

<!--more-->

## Go 函数参数传递到底是值传递还是引用传递

在函数中，如果参数是非引用类型（int、string struct 等这些），这样就在函数中就无法修改原内容数据。

如果参数是引用类型（指针、map、slice、chan 等这些），这样就可以修改原内容数据。

是否可以修改原内容数据，和传值、传引用没有必然关系。在 C++中，传引用肯定是可以修改原内容数据的，在 Co 语言中，虽然只有传值，但是也可以修改原内容数据，因为参数是引用类型。

先说下结论：Go 语言中所有的传参都是值传递（传值），都是一个副本，一个拷贝。

| 类型   | 传递方式 | 说明                                  |
| ---- | ---- | ----------------------------------- |
| 基本类型 | 值传递  | 如 int、float、bool，传递的是数据的副本。         |
| 数组   | 值传递  | 传递数组时，会复制整个数组。                      |
| 结构体  | 值传递  | 传递结构体时，会复制整个结构体。                    |
| 切片   | 值传递  | 切片本身是通过值传递的，但实际上传递的是对底层数组的的引用。      |
| 映射   | 值传递  | 映射（map）同样是引用类型，通过值传递映射时，传递的是对映射的引用。 |
| 通道   | 值传递  | 通道（channel）是引用类型，传递的是对通道的引用。        |
| 接口   | 值传递  | 接口类型本身是通过值传递的，但接口可能持有的是其他引用类型的引用。   |
| 函数   | 值传递  | 函数类型是引用类型，传递的是函数的引用。                |
| 指针   | 值传递  | 指针传递的是指针的副本，但副本执行的是同一个内存地址。         |

### 1、什么是值传递

将实参的值传递给形参，形参是实参的一份拷贝，实参和形参的内存地址不同。函数内对形参值内容的修改，是否会影响实参的值内容，取决于参数是否是引用类型。

### 2、什么是引用传递

将实参的地址传递给形参，函数内对形参值的内容的修改，将会影响实参的值内容。Go 语言是没有引用传递的，在 C++中，函数实参的传递方式有引用传递。

下面分别针对 Go 的值类型（int、struct 等）、引用类型（指针、slice、map、channel），验证是否是值传递，以及函数内对形参的修改是否会修改原内容数据。

### 3、各类型参数传递

#### 1、int 类型

形参和实际参数的内存地址不一样，证明是值传递；参数是值类型，所以函数内对形参的修改，不会修改原内容数据。

```go
package main

import "fmt"

func main() {
    var i int64 = 1
    fmt.Printf("原始 int 内存地址是 %p\n", &i)
    modifyInt(i)
    fmt.Printf("改动后的值是：%v\n", i)
}

func modifyInt(i int64) {
    fmt.Printf("函数里接收到 int 内存地址是 %p\n", &i)
    i = 10
}

原始 int 内存地址是 0x140000a6018
函数里接收到 int 内存地址是 0x140000a6020
改动后的值是：1
```

#### 2、指针类型

形参和实际参数内存地址不一样，证明是值传递，由于形参和实参是指针，指向同一个变量。函数内对指针执行变量的修改，会影响原内容数据。

```go
package main

import "fmt"

func main() {
    var args int64 = 1
    p := &args
    fmt.Printf("原始指针的内存地址是 %p\n", &p)
    fmt.Printf("原始指针指向变量的内存地址是 %p\n", p)
    modifyPointer(p)
    fmt.Printf("改动后的值是：%v\n", *p)
}

func modifyPointer(p *int64) {
    fmt.Printf("函数里接收到指针的内存地址是 %p\n", &p)
    fmt.Printf("函数里接收到指针指向变量的内存地址是 %p\n", p)
    *p = 10
}

原始指针的内存地址是 0x14000050020
原始指针指向变量的内存地址是 0x1400000e0a8
函数里接收到指针的内存地址是 0x14000050030
函数里接收到指针指向变量的内存地址是 0x1400000e0a8
改动后的值是：10
```

#### 3、slice 类型

形参和实际参数内存地址一样，不代表是引用类型；下面进行详细说明 slice 还是值传递，传递的是指针。

```go
func main() {
    var s = []int64{1, 2, 3}
    fmt.Printf("直接对原始切片取地址%v\n", &s)
    fmt.Printf("原始切片的内存地址：%p\n", s)
    fmt.Printf("原始切片第一个元素的内存地址：%p\n", &s[0])
    modifySlice(s)
    fmt.Printf("改动后的值是：%v\n", s)
}

func modifySlice(s []int64) {
    fmt.Printf("直接对函数接接收到切片取地址 %v\n", &s)
    fmt.Printf("函数里接收到切片的内存地址是 %p\n", s)
    fmt.Printf("函数里接收到切片第一个元素的内存地址：%p\n", &s[0])
    s[0] = 10
}


直接对原始切片取地址&[1 2 3]
原始切片的内存地址：0x140000aa018
原始切片第一个元素的内存地址：0x140000aa018
直接对函数接接收到切片取地址 &[1 2 3]
函数里接收到切片的内存地址是 0x140000aa018
函数里接收到切片第一个元素的内存地址：0x140000aa018
改动后的值是：[10 2 3]
```

slice 是一个结构体，他的第一个元素是一个指针类型，这个指针指向的就是底层数组的第一个元素。当参数是 slice 类型的时候，fmt.Printf 通过%p打印的 slice 变量的地址其实就是内部存储数组元素的地址，所以打印出来形参和实参内存地址一样。

```go
type slice struct {
    array unsafe.Pointer // 指针
    len int
    cap int
}
```

因为 slice 作为参数时本质上传递的是指针，上面证明了指针也是值传递，所以参数为 slice 也是值传递，指针指向的是同一个变量，函数内对形参的修改，会修改原内容数据。

单纯的从slice 这个结构体看，可以通过 modify 函数修改存储元素内容，但是永远修改不了 len 和 cap，因此他只是一个拷贝，如果要修改，那就要传递&slice作为参数才可以。

#### #### 4、map 类型

形参和实际参数内存地址不一样，证明是值传递。

```go
package main

import "fmt"

func main() {
    m := make(map[string]int)
    m["age"] = 8

    fmt.Printf("原始 map 的内存地址是：%p\n", &m)
    modifyMap(m)
    fmt.Printf("改动后的值是：%v\n", m)
}

func modifyMap(m map[string]int) {
    fmt.Printf("函数里接收到 map 的内存地址是：%p\n", &m)
    m["age"] = 9
}

原始 map 的内存地址是：0x140000ac018
函数里接收到 map 的内存地址是：0x140000ac028
改动后的值是：map[age:9]
```

通过 make 函数创建的 map 变量本质是一个 hmap 类型的指针*hmap，所以函数内对形参的修改，会修改原内容数据。

```go
// src/runtime/map.go
func makemap(t *maptype, hint int, h *hmap) *hmap {
    mem, overflow := math.MulUintptr(uintptr(hint), t.Bucket.Size_)
    if overflow || mem > maxAlloc {
        hint = 0
    }

    // initialize Hmap
    if h == nil {
        h = new(hmap)
    }
    h.hash0 = uint32(rand())
}
```

#### 5、channel 类型

形参和实际参数内存地址不一样，证明是值传递。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	p := make(chan bool)
	fmt.Printf("原始 chan 的内存地址是：%p\n", &p)
	go func(p chan bool) {
		fmt.Printf("函数里接收到 chan 的内存地址是：%p\n", &p)
		time.Sleep(2 * time.Second)
		p <- true
	}(p)

	select {
	case l := <-p:
		fmt.Printf("接收到的值是：%v\n", l)
	}
}

原始 chan 的内存地址是：0x140000ac018
函数里接收到 chan 的内存地址是：0x140000ac028
接收到的值是：true
```

通过 make 函数创建的 chan 变量本质是一个 hchan 类型的指针*hchan，所以函数内对形参的修改，会修改原内容数据。

```go
// src/runtime/chan.go
func makechan(t *chantype, size int) *hchan {
	elem := t.Elem

	// compiler checks this but be safe.
	if elem.Size_ >= 1<<16 {
		throw("makechan: invalid channel element type")
	}
	if hchanSize%maxAlign != 0 || elem.Align_ > maxAlign {
		throw("makechan: bad alignment")
	}

	mem, overflow := math.MulUintptr(elem.Size_, uintptr(size))
	if overflow || mem > maxAlloc-hchanSize || size < 0 {
		panic(plainError("makechan: size out of range"))
	}
}
```

#### 6、struct 类型

形参和实际参数内存地址不一样，证明是值传递。形参不是引用类型或者指针类型，所以函数内对形参的修改，不会修改原内容数据。

```go
package main

import "fmt"

type Person struct {
	Name string
	Age  int
}

func main() {
	per := Person{
		Name: "test",
		Age: 8,
	}
	fmt.Printf("原始 struct 的内存地址是：%p\n", &per)
	modifyStruct(per)
	fmt.Printf("改动后的值是：%v\n", per)
}

func modifyStruct(per Person) {
	fmt.Printf("函数里接收到 struct 的内存地址是：%p\n", &per)
	per.Age = 10
}

原始 struct 的内存地址是：0x1400000c018
函数里接收到 struct 的内存地址是：0x1400000c030
改动后的值是：{test 8}
```