---
title: "01 - Go 程序是怎么跑起来的"
date: 2023-03-10T19:16:01+08:00
lastmod: 2023-03-10T19:16:01+08:00
draft: false
authors: ["scshark"]
description: ""
tags: ["golang","course"]
categories: ["golang"]
series: ["imooc-golive"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "https://blog.cdn.scshark.com/scshark/2023-04-04/16805755867946.jpg"
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2023-04-04/16805755867946.jpg"

toc:
  enable: true
math:
  enable: false
lightgallery: true
license: ""

---


<!--more-->



## ⼯程师的学习与进步 {#Study-Engineers}

**为什么 Go 语⾔适合现代的后端编程环境？**

- 服务类应⽤以 API 居多，IO 密集型，且⽹络 IO 最多
- 运⾏成本低，⽆ VM。⽹络连接数不多的情况下内存占⽤低。
- 强类型语⾔，易上⼿，易维护。

**为什么适合基础设施？**

- k8s、etcd、istio、docker 已经证明了 Go 的能⼒

---

## 对 Go 的启动和执⾏流程建⽴简单的宏观认识 {#macro-understanding}

### 理解可执⾏⽂件 {#c1}

本节课涉及的⼯具都准备在 `Dockerfile` ⾥了，⼤家可以⾃⾏实验

```dockerfile
FROM centos 
RUN yum install golang -y \ 
 && yum install dlv -y \ 
 && yum install binutils -y \ 
 && yum install vim -y \ 
 && yum install gdb -y
```
运行docker
```
docker build -t test .
docker run -it --rm test bash
```

**接下来开始尝试代码编译**

Go 程序 hello.go 的编译过程：

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-11/16785150559330.jpg" caption="编译过程" >}}


然后我们使⽤ ** `go build -x`**  可以观察这个过程


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-11/16785162242525.jpg" caption="观察编译过程详细信息" >}}

这个过程主要有两个部分组成：

- **编译：⽂本代码 -> ⽬标⽂件(.o，.a)**

- **链接：将⽬标⽂件合并为可执⾏⽂件**

---

**可执⾏⽂件在不同的操作系统上规范不⼀样**

| Linux | Windows | MacOS  |
|-------|---------|--------|
| ELF   | PE      | Mach-O |

我们以Linux 的可执⾏⽂件 **ELF(`Executable and Linkable Format`)** 为例，ELF 由⼏部分构成：

- ELF header 
- Section header 
- Sections

操作系统执⾏可执⾏⽂件的步骤(以 linux 为例)：

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-11/16785165777617.jpg" caption="ELF执行步骤" >}}

通过 **`entry point`** 找到 Go 进程的执⾏⼊⼝，这里需要使⽤ **`readelf`**

> **readelf** elf文件格式分析工具

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-11/16785172201173.jpg" caption="readelf -h ./xxx" >}}

**这样我们就找到了 Go 进程要从哪⾥启动了**


### Go 进程的启动与初始化 {#c2}

计算机是怎么执⾏我们的程序的呢？

接下来我们通过 go 的 一个非常常用的调试工具 **delve（dlv）**  进行调试


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-11/16785176486869.jpg" caption="go dlv 调试过程分析" >}}

CPU ⽆法理解⽂本，只能执⾏⼀条⼀条的⼆进制机器码指令每次执⾏完⼀条指令，**pc 寄存器**就指向下⼀条继续执⾏

在 64 位平台上 **pc 寄存器 = rip**

> **PC 寄存器** 用来存储指向下一条指令的地址，也即将要执行的指令代码。由执行引擎读取下一条指令。

> **%rip** 的名称来自于(`instruction pointer register`，指令指针寄存器)。%rip其实就是程序计数器(Program Counter, PC), 存放着下一条指令的地址 。不可以直接修改%rip。


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-11/16785190264554.jpg" caption="执行过程" >}}

我们可以看到汇编指令的执行过程


**Go 语⾔是⼀⻔有 `runtime` 的语⾔，那么 `runtime` 是什么？**



{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-11/16785202364622.jpg" caption="这是一段关于 runtime 的解析 (Stack Overflow)" >}}


**我们可以认为 `runtime` 是为了实现额外的功能，⽽在程序运⾏时⾃动加载/运⾏的⼀些模块。**


Go可执行文件包含了两部分 （Go Program 和 Runtime）

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-11/16785204193140.jpg" caption="Go可执行文件结构" >}}


**Go 语⾔的 runtime 包括：**

> Scheduler -> 调度器管理所有的 G，M，P，在后台执⾏调度循环

> Netpoll -> ⽹络轮询负责管理⽹络 FD 相关的读写、就绪事件

> Memory Manaement -> 当代码需要内存时，负责内存分配⼯作

> Garbage Collector -> 当内存不再需要时，负责回收内存


这些模块中，最核⼼的就是 **Scheduler**，它负责串联所有的 **runtime** 流程


前面我们通过 **entry point** 找到 Go 进程的执⾏⼊⼝

它会通过三个汇编函数跳到 **runtime.rt0.go** 这个函数里，如图

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-11/16785214816494.jpg"  >}}


**runtime.rt0.go** 这个函数是初始化非常重要的函数（如下图）

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-11/16785216132113.jpg"  >}}

它会 **执行参数的初始化，全局 m0 和 g0 初始化，获取CPU核心数，初始化内置数据结构，开始执行用户main函数**

> **m0: Go 程序启动后创建的第⼀个线程；**


### 调度组件与调度循环 {#c3}

当我每次写下 **goroutine** 的时候，到底发⽣了什么？

```go
go func(){
    println("hello world in goroutine!")
}()
```

你其实是向 **runtime** 提交了⼀个计算任务。

而 `func() { xxxxx }` ⾥包裹的代码就是这个计算任务的内容。


Go 的调度流程本质上是⼀个**⽣产-消费流程**


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-11/16785237450159.jpg" caption="像queue一样" >}}

**go func** 在这里 相当于生产者的角色，你的计算任务会被打包成一个协程 **G （goroutine）**，这个**G** 通过 **queue** 进入**线程 M**，**M** 会一直循环执行消费的操作，中间的**queue** 主要分成两部分， 一个 **local run queue** ，一个 **global run queue**


---

以下是 调度组件 ，那么 **go func** 去了哪里呢？

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-11/16785244827169.jpg" caption="调度组件" >}}

M : machine，系统线程，执⾏实体，想要在 CPU 上执⾏代码，必须有线程，与 C 语⾔中的线程相同，通过系统调⽤ clone 来创建

G : goroutine，⼀个计算任务。由需要执⾏的代码和其上下⽂组成，上下⽂包括：当前代码位置，栈顶、栈底地址，状态等

p : processor，虚拟处理器，M 必须获得 P 才能执⾏代码，否则必须陷⼊休眠(后台监控线程除外)，你也可以将其理解为⼀种 token，有这个 token，才有在物理 CPU 核⼼上执⾏的权⼒。

---

**goroutine 的⽣产端**


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-11/16785256998676.jpg" caption="goroutine 的⽣产端流程" >}}


这里是   {{< link href="https://www.figma.com/proto/gByIPDf4nRr6No4dNYjn3e/bootstrap?page-id=242%3A7&node%02id=242%3A215&viewport=516%2C209%2C0.07501539587974548&scaling=scale-down-width&node-id=6%3A99" content="goroutine 的⽣产端执行流程的动画" >}}，可以更加清晰表现出来


---

**goroutine 的消费端**



{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-11/16785263377252.jpg" caption="goroutine 的消费端流程" >}}

图上有🔒标记的表示处于加锁状态



**globalrunqget** ： 全局队列，**runnableGCWorker**里面的**globalrunqget%61**（取模 %61=0），每60次从全局队列，**global**里面获取一个**goroutine**，从全局获取**goroutine**时，每次拉一半到**local queue **加全局锁，如果不需要执行或没有取出，进入**runqget**

**runqget**：本地无锁队列，优先从**runnext**里面取，如果没有则从**local queue**里面取出，如果没有获取到进入 **findrunnable**

**findrunnable**：先执行上半部分，继续从 **runqget** 获取，没有取到继续往下，**globalrunqget**加锁获取，没有取到继续往下**netpoll**，进入网络轮询找到**goroutine**执行并且放到 **globalrunqget**，没有取到继续往下倒**runqsteal**（**worker stealing**），从其他key获取**goroutine** 进入runqgrab从其他P偷一半G回来。如果上半部分没有获取到G进入下半部分，先再一次获取**globalrunqget**，没有获取到G 进入**check again all runq**，检查所有**runq**，再检查一次**netpoll**，还是没有G的话就把这个M停下进入休眠状态。

**Work stealing** ： **runqsteal -> runqgrab** 这个流程


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786825835388.jpg" caption="循环调度" >}}

**schedtick**：计数器，当前P调度循环执行了多少次，每次执行到**execute** ，**schedtick**就会+1，主要从 globalrunqget 获取 **goroutine**，当 **schedtick** 取模  **%61 ==**0 从**globalrunqget** 获取 **goroutine**


 {{< link href="https://www.figma.com/proto/gByIPDf4nRr6No4dNYjn3e/bootstrap?page-id=143%3A212&node%02id=143%3A213&viewport=134%2C83%2C0.06213996931910515&scaling=scale-down-width&node-id=6%3A99" content="动画" >}}

**仅仅能够处理正常情况是不行的，如果程序中有阻塞，那线程不就被堵上了吗？**

### 处理阻塞{#c4}

在线程发⽣阻塞的时候，会⽆限制地创建线程么？

并不会!!


先来看看阻塞有哪⼏种情况：


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786873936253.jpg" width="250" >}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786874063144.jpg" width="250"  >}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786874112561.jpg" width="250" >}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786874173545.jpg" width="250" >}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786874232962.jpg" width="250" >}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786874527692.jpg" width="250" >}}

这些情况不会阻塞调度循环，⽽是会把 **goroutine** 挂起

所谓的**挂起**，其实让 **g** 先进某个数据结构，待 **ready** 后再继续执⾏, **不会占⽤线程**

这时候，线程会进⼊ **schedule**，继续消费队列，执⾏其它的 **g**





**处理阻塞的各种情况**：

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786883132451.jpg" caption="G阻塞时进入数据结构" >}}






然后是**应⽤阻塞在锁上的情况**：

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786901987414.jpg" caption="应⽤阻塞在锁上的情况" >}}



为啥有的等待是 **sudog**，有的是 **g**?


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786904931081.jpg" caption="贴图解析" >}}

就是说⼀个 **g** 可能对应多个 **sudog**，⽐如⼀个 **g** 会同时 **select** 多个**channel**



前⾯这些都是能被 **runtime** 拦截到的阻塞，但是还有⼀些是 **runtime** ⽆法拦截的：


- **CGO**->在执⾏ **c** 代码，或者阻塞在 **syscall** 上时，**必须占⽤⼀个线程**

需要解决阻塞的问题，我们可以用到 **sysmon: system monitor**


**sysmon 在 runtime有⾼优先级，在专有线程中执⾏不需要绑定 P 就可以执⾏**

**sysmon** 主要工作流程：

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786909739983.jpg" caption="sysmon 主要工作" >}}

## 调度器的发展历史 {#scheduling_history}



{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786913970064.jpg" caption="scheduler 发展历史" >}}


## 知识点总结 {#summary}

> 可执行文件 ELF:
> - 使用 **`go build -x `** 观察编译和链接过程
> - 通过 **`readelf -H`** 中的 **entry** 找到程序入口
> - 在 **dlv** 调试器中 **`b *entry_addr`** 找到代码位置

> 启动流程:
> - 处理参数 -> 初始化内部数据结构 -> 主线程 -> 启动调度循环

> Runtime 构成:
> - **Scheduler**、**Netpoll**、**内存管理**、**垃圾回收**

> GMP:
> - **M**，任务消费者;**G**，计算任务;**P**，可以使用 **CPU** 的 **token**

> 队列:
> - P 的本地 **runnext** 字段 -> P 的 **local run queue** -> **global run queue**，多级队列减少锁竞争

> 调度循环：
> - 线程 M 在持有 P 的情况下不断消费运⾏队列中的 G 的过程。

> 处理阻塞：
> - 可以接管的阻塞：**channel** **收发**，**加锁**，**⽹络连接读/写**，**select**
> - 不可接管的阻塞：**syscall**，**cgo**，⻓时间运⾏需要剥离 **P** 执⾏

> **sysmon**:
> - ⼀个后台⾼优先级循环，执⾏时不需要绑定任何的 P
> - 负责：
>   - 检查是否已经没有活动线程，如果是，则崩溃
>   - 轮询 **netpoll**
>   - 剥离在 **syscall** 上阻塞的 M 的 P
>   - 发信号，抢占已经执⾏时间过⻓的 G
   

## 与调度有关的常⻅问题 {#scheduling_issues}


{{< admonition type=question title="Goroutine ⽐ Thread 优势在哪？" >}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786927447177.jpg" caption="Goroutine and thread" >}}

{{< /admonition >}}

{{< admonition type=question title="Goroutine 的切换成本" >}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786929068557.jpg" caption="gobuf" >}}


**gobuf** 描述⼀个 **goroutine** 所有现场，**从⼀个 g 切换到另⼀个 g**，只要把这⼏个现场字段保存下来，再把 g 往队列⾥⼀扔，m 就可以执⾏其它 g 了，**⽆需进⼊内核态**


{{< /admonition >}}



{{< admonition type=question title="死循环导致进程 hang 死问题" >}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786931683668.jpg" caption="死循环" >}}

GC 时需要停⽌所有 **goroutine**，⽽⽼版本的 Go 的 g 停⽌需要主动让出，**1.14 增加基于信号的抢占之后，该问题被解决**

⽼版本如何定位呢：{{< link href="https://xargin.com/how-to-locate-for-block-in-golang/" content="https://xargin.com/how-to-locate-for-block-in-golang/" >}}
{{< /admonition >}}



{{< admonition type=info title="与 GMP 有关的⼀些缺陷" >}}

创建的 M 正常情况下是⽆法被回收

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786937397036.jpg">}}
 =====> 
{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786937539317.jpg" >}}




怎么解决：{{< link href="https://xargin.com/shrink-go-threads/" content="https://xargin.com/shrink-go-threads/">}}

---


**runtime** 中有⼀个 **allgs** 数组所有创建过的 **g** 都会进该数组⼤⼩与 **g** 瞬时最⾼值相关


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786939523852.jpg" >}}



{{< /admonition >}}


**在 PPT ⾥有各种阻塞场景，你是怎么在代码⾥找到这些阻塞场景的？**

要知道 **runtime** 中可以接管的阻塞是通过 **`gopark/goparkunlock`** 挂起和 **goready** 恢复的，那么我们只要找到 **`runtime.gopark`** 的调⽤⽅，就可以知道在哪些地⽅会被 **runtime** 接管了，你也应该⽤ **IDE** 试⼀试，很简单：

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-13/16786940121866.jpg" >}}


**References** 

**ELF ⽂件解析：** {{< link href="https://github.com/corkami/pics/blob/28cb0226093ed57b348723bc473cea0162dad366/bina elf101/elf101.pdf" content="PDF">}}

**Go Scheduler 变更历史：** {{< link href="https://github.com/golang-design/history#scheduler" content="https://github.com/golang-design/history#scheduler">}}

**Goroutine vs Thread：** {{< link href="https://www.geeksforgeeks.org/golang-goroutine-vs-thread/" content="https://www.geeksforgeeks.org/golang-goroutine-vs-thread/">}}

**Measuring context switching and memory overheads for Linux threads：** 
{{< link href="https://eli.thegreenplace.net/2018/measuring-context-switching-and-memory-overheads-for-linux-thre" content="https://eli.thegreenplace.net/2018/measuring-context-switching-and-memory-overheads-for-linux-thre">}}




---
<center > - END - </center>