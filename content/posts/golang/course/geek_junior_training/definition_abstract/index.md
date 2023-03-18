---
title: "type 定义与 Server 抽象"
date: 2023-03-17T16:46:15+08:00
lastmod: 2023-03-17T16:46:15+08:00
draft: false
authors: ["scshark"]
description: "Go初级训练营第二课 </br> 讲师：Big Ming"
tags: ["golang","course"]
categories: []
series: ["geek-junior-training"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "https://blog.cdn.scshark.com/scshark/2023-03-16/16789676574445.jpg"
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2023-03-16/16789676574445.jpg"

toc:
  enable: true
math:
  enable: false
lightgallery: true
license: ""

---

<!--more-->

## Http 包 {#package-http}


### Request Body {#Request-body}

- **Body**：只能读取一次，意味着你读了别人就不能读了，别人读了你就不能读了;

- **GetBody**：原则上是可以多次读取，但是在 原生的**`http.Request`**里面，这个是 **`nil`**
- 在读取到 **`body`** 之后，我们就可以用于反序列化，比如说将 **`json`** 格式的字符串转化为一个对象等


### Request Query {#Request-query}

- 除了 **`Body`**，我们还可能传递参数的 地方是 **`Query`**
- 也就是 http://xxx.com/your/path?<font color="pink" >id=123&b=456</font>
- 所有的值都被解释为字符串，所以需要自己解析为数字等

### Request URL {#Request-URL}


- {{< image src="https://blog.cdn.scshark.com/scshark/2023-03-17/16790524186160.jpg" width="475" >}}
- 包含路径方面的所有信息和一些很有用的操作
- **URL** 里面 **Host** 不一定有值
- **`r.Host`** 一般都有值，是 **`Host`** 这个 **`header`** 的值
- **`RawPath`** 也是不一定有 
- **`Path`** 肯定有

> Tips：实际中记得自己输出来看一下，确认有没有

### Request Header {#Request-Header}

- **`header`** 大体上是两类，一类是 **`http`** 预定义的，一类是自己定义的
- **Go** 会自动将 **`header`** 名字转为标准名字——其实就是大小写调整
- 一般用 **X** 开头来表明是自己定义的，比如说 **`X-mycompany- your=header`**

### Form {#http-Form}

- **`Form`** 和 **`ParseForm`**
- 要先调用 **`ParseForm`**
- 建议加上 **`Content-Type: application/x-www-form-urlencoded`**

### http包使用要点总结{$pkg-http}

- **`Body`** 和 **`GetBody`**：重点在于 **`Body`** 是一次性的，而 **`GetBody`** 默认情况下是没有(`nil`)，**一般中间件会考虑帮你注入这个方法**
- **`URL`** ：注意 **URL** 里面的字段的含义可能并不如你期望的那样
- **`Form`**：记得调用前先用 **`ParseForm`**，别忘了请求里面加上 **`http`** 头


## 如何使用 Golang Debug? {#use-golang-debug}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-17/16790536724585.jpg" width="520" caption="debug">}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-17/16790538304969.jpg" width="520" caption="断点" >}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-17/16790538980590.jpg" width="520" caption="栈帧-变量">}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-17/16790541033987.jpg" width="520" caption="评估">}}



## type 定义 {#type-definition}

- **type** 定义
    - **`type` 名字 `interface {}`**
    - **`type` 名字 `struct {} `**
    - **`type` 名字 别的类型**
    - **`type` 别名 = 别的类型**
- 结构体初始化
- 指针与方法接收器
- 结构体如何实现接口


### interface 定义 {#interface-definition}

- 基本语法** `type 名字 interface {}`**
- 里面只能有方法，方法也不需要 **`func`** 关键字
- 啥是接口(`interface`)：**接口是一组行为的抽象**
- 尽量用接口，以实现面向接口编程

### struct 定义 {#struct-definition}

- 基本语法:
  ```go
    type Name struct {
        
    // ... }
  ```
- 结构体和结构体的字段都遵循大小写控制访问性的原则

### type A B {#type-a-b}

- 基本语法: 
```go
type TypeA TypeB

```
- 我一般是，在我使用第三方库又没有办法修改源码的情况下，又想在扩展这个库的结构体的方法，就会用这个

### type A = B{#type-a=b}

- 基本语法: 
```go
type TypeA = TypeB

```
- 别名，除了换了一个名字，没有任何区别


### Struct初始化 {#struct-init}

- **Go** 没有构造函数!! 
- 初始化语法：**`Struct{}`**
- 获取指针： **`&Struct{}`**
- 获取指针2：**`new(Struct)`**
- **new** 可以理解为 **Go** 会为你的变量分配内存，并且把内存都置为0


### 指针{#point}


- 和 C，C++ 一样，***表示指针**， **&取地址**
- 如果声明了一个指针，但是没有赋值，那么它是 **`nil`**

### 结构体自引用 {#struct-self-reference}

- 结构体内部引用自己，只能使用 **指针**
- 准确来说，在整个引用链上，如果**构成循环**，那就只能用**指针**
- {{< image src="https://blog.cdn.scshark.com/scshark/2023-03-17/16790557955246.jpg" caption="错误的struct" >}}
- ```go
  // 只能用指针
  type Node struct {
	left *Node
	right *Node
  }
  ```
### 方法接收器 {#struct-func-receiver}


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-17/16790561502009.jpg" >}}

- 结构体接收器内部永远不要修改字段
- 结构体和指针之间的方法可以互相调用
- 设计不可变对象，用结构体接收器，其它用指针

> 总结：遇事不决用指针


### 结构体如何实现接口 {#struct-to-interface}

#### Duck test {#duck}
{{<admonition type=info  title="鸭子定律">}}

著名定律

**wiki** : 即“如果它看起来像鸭子，游泳像鸭子，叫声像鸭子，那么它非常可能就是只鸭子”，告诉我们可以通过观察一未知物的行为特征的方法来辨别这个东西。

当看到一只鸟走起来像鸭子、游泳起来像鸭子、叫起来也像鸭子，那么这只鸟就可以被称为鸭子。

**当一个结构体具备接口的所有的方法的时候，它就实现了这个接口**
{{</admonition>}}

Golan快捷步骤：

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-17/16790571340286.jpg" caption="1">}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-17/16790571602582.jpg" caption="2">}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-17/16790572111061.jpg" caption="3">}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-17/16790572337450.jpg" caption="4">}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-17/16790572154267.jpg" caption="5">}}



### 注释规范 {#Annotation-Standards}
{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-17/16790574077290.jpg"  caption="注释例子">}}


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791157626122.jpg"  caption="注释显示">}}

- 以被注释的开头，后面跟着描述

###  要点总结: type {#type-summary}

- **type** 定义熟记。其中 **`type A=B`** 这种别名，一般只用于兼容性处理， 所以不需要过多关注;
- **先有抽象再有实现，所以要先定义接口**
- 鸭子类型：**一个结构体有某个接口的所有方法，它就实现了这个接口;**
- 指针：**方法接收器，遇事不决用指针;**


##  Http Server —— Server 实现 {#server-realize}

```go

// server.go


func (s *sdkHttpServer) Route( pattern string,
	handlerFunc http.HandlerFunc)  {
	http.HandleFunc(pattern,handlerFunc)
}

func (s *sdkHttpServer) Start(address string) error {
	return http.ListenAndServe(address, s)
}

func NewSdkHttpServer(name string) Server  {
	return &sdkHttpServer{
		Name: name,
	}
}


// main.go

func main(){

    server := web.NewSdkHttpServer("my-test-server")
    //注册路由
    server.Route("/",home)
    
    server.Start("8080")
}


```


##  Http Server —— Context抽象与实现 {#context-realize}

注册的代码虽然可以跑，但是我们可以发现每次要读 **json** 输入就得写很多代码来获取，所以我们可以通过实现 **Context** 抽象来优化注册代码



{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791170127305.jpg"  >}}


**实现context抽象**

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791170220417.jpg"  >}}


## 空接口 interface{} {#interface}

- 空接口 **`interface{}`** 不包含任何方法
- 所以任何结构体都实现了该接口 
- 类似于 **Java** 的 **Object**， 即所谓的继承树根节点

## json 包 {#json-pkg}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791178182830.jpg"  >}}

- 用于处理 **json** 格式的字符串
- 字段后面的内容被称为 **Tag**，即标签，**运行期间可以反射拿到**
- **json**库依据 **`json Tag`** 的内容来 完成**json**数据到结构体的映射
- 典型的声明式API设计


## 写入响应 {#json-resp}



{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791189933876.jpg"  >}}

这部分代码的一些优化问题：

- 强耦合 fmt 库
- 难以输出格式化数据，比如说返
- 回一个 json 数据给客户端 
- 没有处理 http 响应码

这时我们可以把写入响应这个行为抽象出来，如下：


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791191361652.jpg"  >}}

> 这里有个小差异，是我们不再使用 **fmt**，而是直接使用 **Write** 方法
 
然后再进一步封装，提供辅助方法，如下：

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791191846543.jpg"  >}}

> 注意! 它不是 **Context** 本身必须要提供的方法! 即如果你在设计真实的 **web** 框架的时候，**你需要考虑清楚，究竟要不要提供这种辅助方法**

> Tip：严格来说，WriteJson也是辅助方法



我们对比一下和之前的差异：


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791194165125.jpg" width="420" height="300"  >}}


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791194466081.jpg"  width="420" height="300" >}}


## Http Context—— 让 web 框架来创建 context {#create-context}


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791203128839.jpg"   >}}


现在我们看到这里创建**context**，是由用户自己创建的。

在设计过程中，我们应该由框架来创建 **context**，就可以完全控制什么时候 创建，**context** 可以有什么字段。**作为设计者， 这种东西不能交给用户自由发挥**

```go
type Context struct {
	W http.ResponseWriter
	R *http.Request
	PathParams map[string]string
}
```


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791205165879.jpg"  >}}

同时去修改 **Server** 接口

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791205338660.jpg"  >}}


而且原本的 **Router** 方法已经不行了，需要改造

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791205848232.jpg"  >}}


##  要点总结：Server 和 Context {#sumary-server-context}

- 从 **`http.Request`** 中读取数据并解析
- 往 **`http.ResponseWriter`** 中写入数据和响应
- **json** 数据的序列化与反序列化

> 设计是一个循序渐进，逐步迭代，螺旋上升的过程。


## RESTFul API {#restful-api}

### 定义 {#restful-api-def}


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791212439247.jpg"  caption="wiki">}}

简单来说，就是**http method** 决定了操作，**http path** 决定了操作对象




{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791212983849.jpg"  >}}


> **http method + http path = http handler**


我们要加上 **method**


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791213695855.jpg"  >}}


**但是 `http.HandleFunc` 好像不太行，我们需要自己做路由了**

## map {#map}

```go
// 创建了一个预估容量是2的 map
m := make(map[string]string,2)
// 没有指定预估容量
m1 := make(map[string]string)
// 直接初始化
m2 := map[string]string{
        "Name":"scshark",
    }
// 赋值
m2["hello"] = "go"
// 取值
val := m2["hello"]
// 使用两个返回值，后面的ok会告诉你map有没有这个key
val, ok := m["invalid_key"]
if !ok {
    println("key not found")
}
```
- 基本形式：**`map[KeyType]ValueType`**
- 创建 **make** 命令，或者直接初始化
- 取值：**`val, ok := m[key]`**
- 设值：**m[key]=val**
- key 类型：**“可比较”类型**


> Tip：编译器会告诉你能不能做 **key**
> 
> Tip：尽量用基本类型和**string**做**key**，不要和自己过不去

### map 遍历 {#map-for}

- **`for key, val := range m {}`**
- ** Go 一个 for 打天下**
- **Go 的 `map` 的遍历，顺序是不定的 ！！！！**


## 组合 {#combination}

**组合可以是接口组合，也可以是结构体组合。结构体也可以组合接口**


```go
// 以下是 接口组合
// Swimming 会游泳的
type Swimming interface {
	Swim()
}
// 接口组合
type Duck interface {
	// 鸭子是会游泳的，所以这里组合了它
	Swimming
}
// 结构体组合接口 type Duck struct


// 以下是 结构体组合
type Base struct {
	Name string
}

type Concrete1 struct {
	Base
}

type Concrete2 struct {
	*Base
}

func (c Concrete1) SayHello() {
	// c.Name 直接访问了Base的Name字段
	fmt.Printf("I am base and my name is: %s \n", c.Name)
	// 这样也是可以的
	fmt.Printf("I am base and my name is: %s \n", c.Base.Name)

	// 调用了被组合的
	c.Base.SayHello()
}

func (b *Base) SayHello() {
	fmt.Printf("I am base and my name is: %s \n", b.Name)
}
```
### 组合与重写

```go
func main() {
	son := Son{
		Parent{},
	}

	son.SayHello()
}

type Parent struct {

}

func (p Parent) SayHello() {
	fmt.Println("I am " + p.Name())
}

func (p Parent) Name() string {
	return "Parent"
}

type Son struct {
	Parent
}

// 定义了自己的 Name() 方法
func (s Son) Name() string {
	return "Son"
}


```
在这段代码中：
- **Go 没有重写**
- **main** 函数会输出 **I am Parent** 
- 而在典型的支持重写的语言， 如**Java**，我们可以期望它输 出 **I am Son**


> Tip：当你写下类似继承的代码的时候，千万要先试试它会调过去哪个方法

## Handler 抽象 {#handler-abstract}

- 实现一个 Handler，它负责路由
- 如果找到了路由，就执行 **业务代码**
- 找不到就返回 **404**

我们尝试用 **map** 写一个最简单的版本：


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791223738120.jpg"  >}}


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791224018355.jpg"  >}}

但是，这种实现有什么缺点?

- 和实现 **`HandlerBasedOnMap`** 强耦合
- **Route** 方法依赖于知道 **`HandlerBasedOnMap`** 的内部 细节
- 当我们想要扩展到利用路由树来实现的时候，**`sdkHttpServer`** 也要修改

## 重构Handler {#refactoring-handler}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791241006018.jpg"  >}}



**Handler** 和 **Server** 都有 **Route** 方法，就间接说明了，我们需要引入一个新的接口

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791243105839.jpg"  >}}


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-18/16791243230081.jpg"  >}}


> Tip：设计不是凭空而来，而是不断重构而来的


## 小技巧

```go
// 一种常用的Go设计模式
// 用于确保HandlerBasedOnMap肯定实现了这个接口
var _ Handler = &HandlerBasedOnMap{}
```

具体详细去看代码！！


---
<center > - END - </center>

