---
title: "golang接口与实现以及var _ I = (*T)(nil)的意义"
date: 2022-12-12T15:51:14+08:00
lastmod: 2022-12-12T15:51:14+08:00
draft: false
authors: ["scshark"]
description: ""
tags: ["golang","interface","golang features"]
categories: ["Golang"]


hiddenFromHomePage: false
hiddenFromSearch: false


toc:
  enable: true
math:
  enable: false
lightgallery: true
license: ""

---

<!--more-->

**今天在看书的时候发现了这么一句话`var _ HelloServiceInterface = (*HelloServiceClient)(nil)`，其中`HelloServiceClient`是一个结构体，另外一个自然是一个`interface`。代码的作者称这行代码要求后面的结构体必须实现前面的接口，不然会出现编译错误。**

说实话，我第一眼真的没反应过来。直到我看到了这个问题 **这一行实际上是变量赋值的变体`var VariableName variableType = variableValue`，其中的值是`(*HelloServiceClient)`类型，实际值为`nil`。**

另外有类似的语句

``` golang
//T is a struct
var _ I = T{}       // Verify that T implements I.
var _ I = (*T)(nil) // Verify that *T implements I.
```

这个问题的本质其实是接口的定义：**结构体只要实现了接口的定义，它就能被赋予该接口的变量。** 

对于指针来说就要更复杂一些，详见#180 。

**概要来说，就是对用指针实现的接口，该指针类型的普通结构体不能使用**，具体原因也比较复杂（详见Go程序设计与实现-接口相关章节）


{{< image src="https://blog.cdn.scshark.com/scshark/2022-12-12/16708319071498.jpg" caption="Go程序设计与实现 - 指针和接口的解析">}}
