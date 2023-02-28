---
title: "关于"
subtitle: ""
date: 2023-02-27T13:34:16+08:00
draft: false
comment: false
---

{{< style "min-height: 230px;" >}}
{{< typeit code=golang >}}
package main

import "fmt"

type Blog struct {
    Name   string `json:"name"`
    Author string `json:"author"`
    Url    string `json:"url"`
}

func main() {
    blog := Blog{
        Name:   "一个PHP菜鸟的心路历程",
        Author: "王二愣子",
        Url:    "https://blog.yingnan.wang",
    }
    fmt.Println(blog.Name)
}

{{< /typeit >}}
{{< /style >}}

> 一个混迹于北京的程序猿，主要从事`Golang`、`PHP`和`Java`相关的开发工作。

## 编程领域

- `Golang`、`PHP`，主要用它进行 web 开发
- `Java`个人业余爱好
- `MySQL`、`MongoDB`

## 个人信息

### 自我评价

- 热爱学习和使用新技术；
- 有着十分强烈的代码洁癖；
- 喜欢重构代码，善于分析和解决问题；

### 个人爱好

- 编程
- 学习

### 联系方式

- **Email**: wang2lengzi@88.com
- **QQ**: 1258152932