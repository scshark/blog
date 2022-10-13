---
title: "BTC-密码学原理(01-02)"
date: 2022-09-26T10:42:29+08:00
lastmod: 2022-09-26T10:42:29+08:00
draft: false
authors: ["scshark"]
description: "Cryptocurrency在加密学中最主要的两个功能：Hash，Sign"
tags: ["Blockchain","Course","Hash","BTC","Cryptocurrency","Cryptographic"]
categories: ["Blockchain"]
series: ["bju-openclass-blockchain"]
series-weight: 1
hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "https://blog.cdn.scshark.com/scshark/2022-10-02/16647047390592.jpg"
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2022-10-02/16647047390592.jpg"

toc:
  enable: true
math:
  enable: true
lightgallery: false
license: ""

---

比特币被称为加密货币 **Crypto Currency**

<!--more-->


## Crypto Currency {#crypto-currency}

区块链上内容都是公开的，包括区块的地址，转账的金额。

比特币主要用到了密码学中的两个功能：1.**哈希** 2.**签名**

## 哈希 Hash {#hash}

密码学中用到的哈希函数被称为**cryptographic hash function**

现在应用的 SHA256 ，即 256位哈希值，输出空间为 $2^{256}$

它有两个重要的性质：**collision resistance** 和 **hiding**

### collision resistance {#collision_resistance}

**collision(这里指哈希碰撞) resistance**  :
例如x≠y H(x)=H(y) 两个不同的输入，输出却是相等的，这就称哈希碰撞。它是不可避免的，因为输入空间总大于输出空间。给出x，很难找到y，除非蛮力求解(brute-force)。蛮力求解在实际应用中其实是不可行的，因为工作量非常非常大，超级计算机几百万年才能破解。

> $ x≠y $ ，   $ H(x)=H(y) $

该性质的作用：对一个message求[digest]^(摘要)

比如message取m,m的哈希值是H(m)=digest 如果有人想篡改m值而H(m)不变，则无法做到

哈希碰撞无法人为制造，无法验证，是根据实践经验得来的。

### hiding property {#hiding_property}

**hiding 哈希函数的计算过程是单向的，不可逆的。**

(从H(x)无法推导出x) `hiding` 性质前提是输入空间足够大，分布比较均匀。如果不是足够大，一般在x后面拼接一个随机数，如H(x||nonce)。

该性质的作用和`collision resistance` 结合在一起，用来实现`digital commitment (数字委托书)`(又称为`digital equivalent of a sealed envelope (数字版的密封信封)`)

把预测结果作为输入x，算出一个哈希值，讲哈希值公布，`hiding`让人们知道哈希值而不知道预测值，最后再将x公布，因为有`collision resistance`的性质，预测结果是不可篡改的。

除了密码学中要求的这两个性质外，比特币中用到的哈希函数还有**第三个性质**:

### puzzle friendly {#puzzle_friendly}

**puzzle friendly 指哈希值的预算事先是不可预测的。**

假如哈希值是00...0XX...X，一样事先无法知道哪个值更容易算出这个结果，还是要一个一个代入。

比特币挖矿的过程中实际就是找一个`nonce`，`nonce`跟区块的块头(`block header`)里的其他信息合一起作为输入，得出的哈希值要小于等于某个指定的目标阈值。

> H(block header)≤target

`block header`  指块头，块头里有很多域，其中一个域是我们可以设置的随机数`nonce`，挖矿的过程是不停的试随机数，使得`block header`取哈希后落在指定的范围之内。

`puzzle friendly`是指挖矿过程中没有捷径，为了使输出值落在指定范围，只能一个一个去试。所以这个过程还可以作为工作量证明`(proof of work) - POW`。

挖矿很难，验证很容易。(`difficult to solve ,but easy to verify - 难以解决，但易于验证`)

比特币中用的哈希函数叫作`SHA-256`(`secure hash algorithm - 安全哈希算法`)以上三个性质它都是满足的。

## 签名 Sign {#sign}

在比特币系统中开账户:
在本地创立一个公私钥匙对(`public key` ,`private key`)，这就是一个账户。公私钥匙对是来自于非对称的加密技术(`asymmetric encryption algorithm`)。

两人之间信息的交流可以利用密钥(`encryption key`)，A将信息加密后发给B，B收到后用密钥解密，因为加密和解密用的是同一个密钥，所以叫**对称加密**。前提是有渠道可以安全地把密钥分发给通讯的双方。因此对称加密的缺点就是密钥的分发不方便，因为在网络上很容易被窃听。**非对称密钥**是用一对密钥而不是一个，**加密用公钥，解密用私钥**，加密和解密用的都是接收方的公钥和私钥。公钥是不用保密的，私钥要保密但是私钥只要保存在本地就行，不用传给对方。公钥相当于银行账号，别人转账只要知道公钥就行，私钥相当于账户密码，知道私钥可以把账户上钱转走。公钥和私钥是用来签名。

假如A想向B转10个比特币，A把交易放在区块链上，别人怎么知道这笔交易是A发起的呢?这就需要A要用自己的私钥给交易签名，其他人收到这笔交易后，要用A的公钥去验证签名。签名用私钥，验证用公钥，用的仍然是同一个人的。创建账户产生相同公私钥的可能性微乎其微，所以大量创建账户来窃取其他人账户是不可行的。

我们假设产生公私钥时有一个好的**随机源**(`a good source of randomness`)，产生公私钥是随机的，如果随机源不好，就有可能产生相同的公私钥。比特币中用的签名算法，不仅是生成公私钥的时候要有好的随机源，之后每一次签名时也要有好的随机源。只要有一次签名用的随机源不好的话，就有可能泄露私钥。

---

{{< bilibili BV1Vt411X7JF 2 >}}

---
<center > - END - </center>



