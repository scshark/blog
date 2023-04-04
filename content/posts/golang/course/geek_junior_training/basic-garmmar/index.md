---
title: "Golang 基础语法和 Web 框架起步"
date: 2023-03-16T19:48:08+08:00
lastmod: 2023-03-16T19:48:08+08:00
draft: false
authors: ["scshark"]
description: ""
tags: ["golang","course"]
categories: ["golangyarn"]
series: ["geek-junior-training"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "https://blog.cdn.scshark.com/scshark/2023-04-04/16805752734407.jpg"
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2023-04-04/16805752734407.jpg"

toc:
  enable: true
math:
  enable: false
lightgallery: true
license: ""

---


<!--more-->


##  关于本课程 {#about_course}
- **掌握Go的基本语法**
    - 类型、方法、控制结构
    - 接口与结构体
    - 基本库与并发编程入门

- **掌握GoWeb框架设计要点**
    - 如何设计路由树
    
- **掌握Go常用的设计模式**

##  基础语法{#basic_garmmar}


### main 函数概览 {#main_desc}


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-16/16789682975077.jpg" width="520">}}

- 无参数、无返回值
- main 方法必须要在 main 包里面
- `go run main.go` 就可以执行
- 如果文件不叫 `main.go`，则需要`go build` 之后再 `go run`


### 包声明 {#package_desc}

- 语法形式：**package xxxx**
- 字母和下划线的组合
- 可以和文件夹不同名字
- 同一个文件夹下的声明一致

> Tip：使用**Goland**来创建文件夹，它会自动加上**package**

- 引入包语法形式：**`import [alias] xxx`**
- 如果一个包引入了但是没有使用，会报错
- 匿名引入：前面多一个下划线 `_`

### string类型 {#string}

- 双引号引起来，则内部双引号需要使用\转义 
- \`引号引起来，则内部\`需要\转义

```go
print("Hello \"scshark\" Go ")
print(`Hello \"scshark\" Go 
			可以换行
可以换行
`)
//console Output
//Hello "scshark" Go  
//Hello \"scshark\" Go 
//                        可以换行
//可以换行

```

- **string** 的长度很特殊：
    - 字节长度：和编码无关，用 **`len(str)`** 获取
    - 字符数量：和编码有关，用编码库来计算

    
```go
	println(len("大沙井")) //输出 9
	println(utf8.RuneCountInString("大沙井")) //输出 3
	println(utf8.RuneCountInString("大沙井abc"))//输出 6
```

> 如果你觉得字符串里边会出现非 ASC II 的字符，就记得 用 utf8 库来计算“长度”


- **string** 的拼接直接使用 + 号就可以。注意的是，某些语言支持 **string** 和别的类型拼接，但是 **golang** 不可以
- **strings** 主要方法(你所需要的全部都可以找到):
    - 查找和替换 
    - 大小写转换
    - 子字符串相关 
    - 相等

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-16/16789702864791.jpg" width="375" caption="strings" >}}

### rune 类型 {#rune}


**rune，直观理解，就是字符**

- `rune`不是 `byte`!
- `rune` 本质是int32，一个`rune` 四个字节
- `rune`在很多语言里面是没有的，与之对应的是，**golang** 没有 `char` 类型。`rune` 不是数字，也不是 `char`，也不是 `byte`!
- 实际中不太常用

### bool, int, uint, float 家族 {#other_basic_type}

- bool：true , false
- int8 , int16 , int32 , int64 , int
- uint8 , uint16 , uint32 , uint64 , uint 
- float32 , float64

### byte 类型 {#byte}

**byte，字节，本质是 `uint8`**

对应的操作包在bytes上

### 类型总结 {#type_summary}

- **golang的数字类型明确标注了长度、有无符号**
- **golang不会帮你做类型转换，类型不同无法通过编译。也因此，`string`只能和`string`拼接**
- **golang有一个很特殊的`rune`类型，接近一般语言的`char`或者`character`的概念，非面试情况下，可以理解为 “`rune` = 字符”**
- **`string`遇事不决找`strings`包**


## 变量声明{#Variable-declaration}

### var {#var}
- **var** ， 语法：**`var name type = value`**
- 局部变量
    - 包变量
    - 块声明
- 驼峰命名
- 首字符是否大写控制了访问性：大写包外可访问；
- **golang**支持类型判断

### := {#v}

- 只能用于局部变量，即方法内部
- **golang**使用类型推断来推断类型。数字会理解为int或者float64.（所以要其他类型的数字，就得用**`var`**来声明）


### 变量声明易错点{#mistakes}

- 变量声明了没有使用
- 类型不匹配
- 同作用域下，变量只能声明一次

### 常量声明 const {#const}

- 首字符是否大写控制了访问性：大写包外可以访问
- 驼峰命名
- 支持类型推断
- 无法修改值

## 方法声明与调用 {#func-statement-call}

### 方法声明 {#func-statement}

- 四个部分
    - 关键字 **`func`**
    - 方法名字： 首字母是否大写决定了作用域
    - 参数列表： [\<name type\>]
    - 返回列表： [\<name type\>]

> 推荐写法：
> 参数列表含有参数名
> 返回值不具有返回值名
> - 个人偏好


### 方法调用 {#func-call}

- 使用 `_` 忽略返回值

### 方法声明与调用总结 {#func-statement-call-summary}

- golang 支持多返回值，这是一个很大的不同点
- golang 方法的作用域和变量的作用域一样，通过首字符大小写控制
- golang 的返回值是可以有名字的，可以通过给予名字让调用方清楚知道你返回的是什么



## fmt格式化输出 {#fmt-output}

fmt 包有完整的说明

- 掌握常用的 ： **`%s ,  %d ,  %v ,  %t , %+v, %#v`**
- 不仅仅是`fmt`的调用，所有格式化字符串的API都可以用
- 因为**golang**字符串拼接只能在**string**之间，所以这个包非常常用

## 数组和切片 {#array-slice}

数组和别的语言的数组差不多，语法是 **`[cap]type`**

1、初始化要指定长度（或者叫做容量）
2、直接初始化
3、**`arr[i]`** 的形式访问元素
4、**len**和**cap**操作用于获取数组长度

> Tip：数组的len和cap结果是一样的，就是数组长度


切片，语法 **`[]type`**

- 1、直接初始化
- 2、make初始化 ： **`make([]type,length,capacity)`**
- 3、arr[i]形式访问元素
- 4、append追加元素
- 5、len获取元素数量
- 6、cap获取切片容量
- 7、推荐写法：**`s := make([]type,0,capacity)`**

> Tip：append追加元素超过切片容量，切片会自动扩容，扩容的时候默认扩容2倍长度（前提<256）如果超过当前容量的2倍，那么扩容容量就是实际长度的偶数值（如果长度是奇数，那么就加1），如果容量超过256，实际执行1.25x扩容，直到容量足够存储


    
|              | 数组   | 切片         |
|--------------|--------|--------------|
| 直接初始化   | 支持   | 支持         |
| make         | 不支持 | 支持         |
| 访问元素     | arr[i] | arr[i]       |
| len          | 长度   | 已有元素个数 |
| cap          | 长度   | 容量         |
| append       | 不支持 | 支持         |
| 是否可以扩容 | 不可以 | 可以         |


### 子切片 {#child-slice}

数组和切片都可以通过**`[start:end]`** 的形式来获取子切片：

- 1、**`arr[start:end]`**，获得**`[start,end]`**之间的元素
- 2、**`arr[:end]`** ，获得 **`[0,end]`**之间的元素
- 3、**`arr[start:]`**，获得**`[start,len(arr)]`**之间的元素

### 如何理解切片 {#understand-slice}

最直观对比：Arraylist，即基于数组的List的实现，切片的底层也是数组

跟ArrayList的区别：

- 切片的操作时有限的，不支持随机任意增删（即没有add,delete，需要自己写代码）
- 只有append操作
- 切片支持子切片操作，和原本切片共享底层数组

### 共享底层 {#optional}

子切片和切片究竟会不会互相影响，就抓住一点：**他们是不是共享数组？**

**就是说他们结构没有变化，那肯定是共享的，但是结构变化了，就可能不共享了**


## 基础语法 for {#for}

**for** 和别的语言差不多，有三种形式

- **`for{}`** ，类似**`while`**的无限循环
- **`fori`**，一般的按照下标循环
- **`for range`** 最为特殊的range遍历
- **break** 和 **continue** 和别的语言一样

## 基础语法 switch {#switch}

switch 和别的语言也差不多

switch 后面可以是基础类型和字符串，或者满足特定条件的结构体

最大差别：不用加break

> Tip ： 大多数时候，switch后面只会用基础类型或者字符串


## 要点总结 {#t-summary}

- 1、 string 类型 ———— 和别的语言没啥区别
- 2、 基础类型 ———— 不必死记硬背，Goland IDE 会提示你
- 3、 切片 ———— make，[i]，len，cap，append
- 4、 数组 ———— 和别的语言没啥区别
- 5、 for，if，switch ———— 和别的语言区别也不大，IDE可以提示

## 配置Goland - go mod 管理依赖 {#config-goland}



{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-17/16790334227242.jpg" >}}


---
<center > - END - </center>
