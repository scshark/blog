---
title: "BTC-比特币网络(06)"
date: 2022-11-08T13:57:01+08:00
lastmod: 2022-11-08T13:57:01+08:00
draft: false
authors: ["scshark"]
description: "比特币工作在应用层，它的底层是一个网络层"
tags: ["Robust","best effort","拓扑结构"]
categories: ["Blockchain"]
series: ["bju-openclass-blockchain"]
series-weight: 5


hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "https://blog.cdn.scshark.com/scshark/2023-04-04/16805757940288.jpg"
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2023-04-04/16805757940288.jpg"

toc:
  enable: true
math:
  enable: true
lightgallery: false


---


<!--more-->


### 比特币网络 {#btc-network}

比特币工作在**应用层**(`application layer`:**`Bitcoin block chain`**)，它的底层是一个**网络层**(`network layer`:`P2P overlay network`)。

**比特币的P2P网络是非常简单的，所有节点都是对等的**。不像有的P2P网络有所谓的**超级节点**、**纸节点**。


要加入**P2P网络**首先得知道至少有一个种子节点，然后你要跟种子节点联系，它会告诉你它所知道的网络中的其他节点，节点之间是通过`TCP`通信的，这样有利于穿透防火墙。当你要离开时不需要做任何操作，不用通知其他节点，退出应用程序就行了。别的节点没有听到你的信息，过一段时间之后就会把你删掉。

### 设计原则 {#design—principle}

比特币网络的设计原则是：

**简单、鲁棒(`鲁棒是Robust的音译，也就是健壮和强壮的意思。它也是在异常和危险情况下系统生存的能力。`），而不是高效**。每个节点维护一个零度节点的集合，消息传播在网络中采取`flooding`的方式。节点第一次听到某个消息的时候，把它传播给去他所有的**零度节点**，同时记录一下这个消息我已经收到过了。下次再收到这个消息的时候，就不用转发给零度节点了。

**零度节点**的选取是随机的，没有考虑底层的**拓扑结构（`拓扑结构是指网络中各个站点相互连接的形式`）**。

> 比如一个在加利福尼亚的节点，它选的零度节点可能是在阿根廷的。这样设计的好处是**增强鲁棒性**，它没有考虑底层的**拓扑结构**，但是牺牲的是**效率**，你向身边的人转账和向美国的人转账速度是差不多的。


### 冲突交易 {#clash_bs}
比特币系统中，每个节点要维护一个等待上链的交易的集合。假如一个集合的交易都是等待写入区块链里的，那么第一次听到某个交易的时候，把这个交易加入这个集合，并且转发这个交易给节点，以后再收到这个交易就不用转发了，这样避免交易会在网络上无限的传播下去。**转发的前提是该交易是合法的**。


> 这里有冲突的情况，有可能你会有两个有冲突的交易，差不多同时被广播到网络上。比如说`A→B`和`A→C`，这两个如果同时广播在网络上，那么每个节点根据在网络中的位置的不同，收到两个交易的先后顺序不同。
> 
> ⬇️
> 
> 比如一个人先收到第一个交易，就写入到集合里，再收到第二个交易的时候就不会写入集合，因为跟上一个交易有冲突，就认定是**非法**的。假设这两个交易花的是同一个币，那么写入集合的交易就会被删掉。
> 
> ⬇️
> 
> 比如说节点听到一个新发布的区块，里面包含了`A→B`的交易，那么这个交易就可以删掉了，因为已经写入到了区块链里。如果节点又听到了`A→C`的交易，该怎么办?这时候也要把A→B删掉。因为A→C如果已经被写入到了区块里，那么A→B就变成了非法交易，就变成了`double spending`，这就是冲突的情况。**可能某个先收到A→C的节点，抢先挖到了矿，发布了区块。**

### 网络传播 {#node_spread}

新发布的区块在网络上的传播有很多方式，跟新发布的交易是类似的。**每个节点除了要检查区块的内容合法性之外，还要查它是不是在最长合法链里。越是大的区块，在网络上传播速度越慢。**

**比特币协议对区块的大小有1M字节的限制**。比特币系统采用的传播方式是非常耗费带宽的，带宽是瓶颈。按1M的区块大小限制来算的话，一个新发布的区块有可能需要几十秒，才能传输到网络大部分境地，这已经是挺长时间了，所以**这个限制值不算小**。

⚠️**还需要注意的一点：我们讲的比特币网络的传播属于`best effort`** 。
> 一个交易发布到比特币网络上，不一定所有的节点都能收到，而且不同的节点收到这个交易的顺序也不一定是一样的。网络传播存在延迟，而且这个延迟有的时候可能会很长，有的节点也不一定按照比特币协议的要求进行转发。
> 
> **可能有的该转发的不转发，导致某些合法的交易收不到，也有的节点可能转发一些不该转发发的消息，比如说有些不合法的交易也被转发了**。这就是我们面临的一个 **`实际问题`**。




---


{{< bilibili BV1Vt411X7JF 6 >}}

---
<center > - END - </center>





