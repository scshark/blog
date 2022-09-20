---
title: "我的第一个NFT - 加密货币基础"
date: 2022-09-15T19:45:54+08:00
lastmod: 2022-09-15T19:45:54+08:00
draft: false
authors: ["scshark"]
description: "欢迎来到《我的第一个 NFT》，在这里你可以铸造一个免费的 NFT"
tags: ["Bitcoin","Ethereum","Blockchain"]
categories: ["NFT"]
series: ["my-first-nft"]
series-weight: 0

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "https://blog.cdn.scshark.com/scshark/2022-09-16/16633248029037.jpg"
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2022-09-16/16633248029037.jpg"

toc:
  enable: true
math:
  enable: false
lightgallery: true

---

<!--more-->

> ## 区块链是什么？
>  {{< typeit >}} **在介绍加密货币之前，我们不得不提到加密货币的底层技术——区块链** {{< /typeit >}}

### 这是一个区块 {#block}
* 每一个区块包括两个部分：交易记录等一些信息, 和之前的区块摘要（哈希）。
* 每一个区块只有有限的存储容量。
* 每个区块会在一定时间内打包，例如大概 10 分钟打包生成一个。

{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-16/16633083307165.jpg" caption="block" width="300" height="350">}}

### 这是一条链 {#chain}
* 一条链只可以链接两个区块。
* 只有一条链。
* 区块按照顺序依次串联。

{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-16/16633090717655.jpg" caption="chain" width="300" height="350">}}

### 合并在一起就是：<font color="#377dff"> **区块链** </font> {#blockchain}

{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-16/16633098076921.jpg" caption="blockchain" >}}


### 由此得出区块链特性 {#blockchain-character}

> #### 不可篡改
> 你无法改变历史信息，因为每个区块包含了上一个区块的摘要并串联起来，如果你修改了历史的区块，你将必须修改后面的全部区块。
> #### 公开透明、匿名
> 在区块链上的信息全部公开透明。每个人都可以顺着区块和链找到历史上所有的记录来查看你的钱包余额。但是没人知道这是你的钱包。
> #### 快速交易
> 无论金额多少以及你在什么地方，只要你的交易记录被打包在区块链中，交易就自动完成。相比传统的跨国汇款非常快速便捷。

{{< admonition type="question" title="🤔️🤔️🤔️🤔️🤔️" >}}
**那么如何来确保历史交易记录真的没有被篡改而且可信呢？**
{{< /admonition >}}


### 分布式记账！{#distributed-accounting}

{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-16/16633107498588.jpg" caption="分布式记账" >}}

<center>
<font size="4">一个区块链网络中有非常多的节点（矿机）来记账，每个节点都会记录完整的、相同的区块链信息！</font>
</center>


### 有了分布式网络之后，区块链有了新特性 {#blockchain-new-character}

> #### 去中心化
> 区块链网络通常分布在全球，每个节点都将会存储一份相同的区块链数据。没有人能够控制全部的节点，因此这份区块链数据将会一直存在。
> #### 真正的不可篡改
> 区块链网络通常分布在全球，一个人控制大部分节点几乎不可能，因此即便有人修改了部分节点的区块链数据，只要被修改记录的节点不超过 51%，这个改动将不会被认可。

--- 

## 什么是比特币（BTC）？{#what_is_btc}

</br>
<center style="font-size:20px;color:#525f6c"> 
<strong>去中心化和分布式网络很好，可是为什么会有人愿意提供这项服务呢？</br></br>
因为他们可以因此得到奖励：<font color="#377dff">比特币</font> !</strong>
</center>

{{< admonition type="abstract" title="BTC" >}}
 ### 节点可以得到奖励 {#node_award}

 网络节点服务提供商（以下简称为网络服务提供商）可以得到奖励。不同的网络服务提供商可以得到不同的代币奖励，比如：比特币。
 
 {{< image src="https://blog.cdn.scshark.com/scshark/2022-09-16/16633149405424.jpg" caption="节点奖励">}}
 
 ### 比特币具备了货币属性 {#btc_currency_properties}
 根据比特币的设计，它仅有有限的供应量，而且可以自由转账。因此具备了货币的特性，成为了加密货币。                                                 
    </br> 
 {{< image src="https://blog.cdn.scshark.com/scshark/2022-09-16/16633149155053.jpg" caption="货币系统" >}}
{{< /admonition >}}

### 对于比特币的一些疑问 {#btc_question}
{{< admonition type="question" title="FAQ" >}}

**比特币有什么价值？**

它具有货币的属性，按照白皮书的设计，不会通胀。同时根据区块链的特性它还具备以下特点：账户匿名、交易公开透明、不可篡改、完全在虚拟网络中，相比跨境转账结算等场景要方便许多。因此具备了一定的使用价值。

***

**为什么比特币会有如此高切不稳定的价格？**

它的价格由供需决定，与大白菜、股票、货币没有区别。只不过没有监管和限制，容易受到热点新闻的影响出现大幅度波动。

***

**区块链如何实现交易公开透明同时保护隐私并且匿名呢？**

在整个网络中，你的交易将通过一个随机生成的钱包地址完成，与这个钱包地址相关的交易等在网络上全部是公开透明的。匿名是因为别人不知道你跟这个钱包有关联。如果你在社交媒体发布过这个钱包地址，那么就可以将其跟你联系在一起。

***

**比特币有什么缺点？**

匿名和自由有利也有弊。弊端在于难以追踪和限制，所以经常被黑产利用，进行洗钱等违法犯罪活动。由于每打包一个区块需要约 10 分钟，因此也会影响交易的实时性。每个区块的存储数据也是有限的。不过，越来越多的新区块链技术正在优化和解决这些问题。

 </br>
{{< /admonition >}}


---

## 区块链的核心组成部分 {#blockchain_core}
 </br>
<center style="font-size:20px;color:#525f6c">
<b>
由上面区块链和比特币内容我们可以得知区块链由两个核心组成
 </br>
 </br>
</b>

</center>


>### 去中心化的网络和区块链 {#decentralized_blockchain}
> * 区块链将会有一条链来记录全部的信息，这条链将存在对应的去中心化网络中。
> * 去中心化的网络，将由无数节点提供服务来维持网络运行。这种操作称之为挖矿。
>  </br>
>  </br>
>  {{< image src="https://blog.cdn.scshark.com/scshark/2022-09-16/16633234466516.jpg" caption="node" width="400" heigth="250">}}
> 
>### 维持网络运行的代币激励 {#node_coin}
> * 去中心化的网络由无数节点提供服务来维持网络运行，这种操作称之为挖矿。维持这些服务的人一般称之为矿工。矿工们维持网络运行需要奖励，就像你工作需要工资，在区块链中对矿工的奖励一般是代币，你经常听说的 Gas Fee 就是矿工们的「工资」。
> * 你使用这个网络进行交易、转账、铸造 NFT等等，均需要支付代币。如果你没有代币又想使用这个区块链网络服务，则需要进行代币之间交换或者法币交换。
>  </br>
>  </br>
>  {{< image src="https://blog.cdn.scshark.com/scshark/2022-09-16/16633242388608.jpg" caption="coin" width="400" heigth="400">}} 

### 一条区块链如何运行起来呢？{#how_blockchain_run}


{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-16/16633243248699.jpg" caption="blockchain ecosystem" >}} 

<center>
由于区块链结点的存在，让区块链技术与特性得以维持，从而聚集了大量的开发者基于此来研发应用，有了项目就能让用户聚集与活跃，由此产生的交易代币滋养了区块链结点。
</center>


---


## 以太坊和以太币（ETH）是什么？ {#what_is_ethereum}


{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-16/16633249560267.jpg" caption="Ethereum" >}}

<center style="font-size:19px">
<b>
以太坊是由社区驱动的技术，为加密货币以太币（ETH）和成千上万的去中心化应用程序提供动力。
</b>
</center>
</br>

### 以太坊是一条区块链 {#ethereum_is_blockchain}
以太坊推出了智能合约的功能，简单的说，开发者可以在以太坊上基于智能合约和协议开发各类应用。其中 NFT 就是一个重要的应用，而且主流的 NFT 项目大部分在以太坊网络发布。
{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-16/16633234466516.jpg" caption="node" width="400" heigth="250">}}


### 以太币（ETH）是其代币 {#eth_is_that_coin}
在以太坊上进行的任何操作或者交易，需要使用以太币（ETH）来支付相关费用。

{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-16/16633253907553.jpg" caption="ethereum">}}


<center>由于区块链是去中心化的，因此在区块链上的操作都需要支付手续费给网络服务提供商。
这个手续费通常称为 Gas Fee（正如你开车到达目的地时，需要燃烧汽油一样）。
</center>



