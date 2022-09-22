---
title: "我的第一个NFT - 铸造我的第一个 NFT"
date: 2022-09-20T15:55:03+08:00
lastmod: 2022-09-20T15:55:03+08:00
draft: false
authors: ["scshark"]
description: "欢迎来到《我的第一个 NFT》，在这里你可以铸造一个免费的 NFT"
tags: ["NFT","MetaMask","Web3","Blockchain","ETH","Wallet","OpenSea"]
categories: ["NFT"]
series: ["my-first-nft"]
series_weight: 3
hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "https://blog.cdn.scshark.com/scshark/2022-09-22/16638156825739.jpg"
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2022-09-22/16638156825739.jpg"

toc:
  enable: true
math:
  enable: false
lightgallery: true


---

<!--more-->


## Step1: 获取以太坊钱包 {#step_one}

**目前最流行的以太坊钱包是 MetaMask（{{< link "https://metamask.io" "https://metamask.io/" >}}），下面将介绍如何安装和创建钱包。推荐使用 Chrome 浏览器。**


### :one: **安装 Chrome 浏览器插件** 16638167507066

打开 MetaMask 官方下载页面 {{< link "https://metamask.io/download" "https://metamask.io/download" >}}，选择 Chrome 并点击 “Install MetaMask for Chrome” 按钮。
{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638157927896.jpg"  >}}


然后在 chrome web store 点击 “Add to Chrome” 按钮，安装 MetaMask。

{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638158965901.jpg"  >}}



### :two: **创建钱包** {#create_wallet}

安装完成之后，点击 MetaMask 插件会弹出来创建钱包的引导页面。我们在这里选择 “Create a wallet”。
{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638159203122.jpg"  >}}

输入密码之后，点击 “Create” 按钮。

{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638159298110.jpg"  >}}


妥善保管你的助记词，这将是你最重要的信息。丢失助记词将导致你的钱包丢失！！！我们将在下一节介绍安全防护知识。

{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638159411436.jpg"  >}}


点击 Next 并且验证了你的助记词之后，以太坊钱包就创建完成了。


### :three: 获取钱包地址等信息 {#get_wallet_info}

钱包创建成功之后，点击 MetaMask 你将可以看到下面钱包界面。钱包展示了以下信息：当前区块链网络（Ethereum Mainnet，以太坊主网）、账号名称（Account 1）、钱包地址（0xd12...C7D5）、当前钱包的余额以及一些常用操作按钮等。

{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638159635709.jpg"  >}}



比较常用关键的信息就是你的钱包地址，你可以点击 “Account 1” 来复制你的完整钱包地址。

{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638159708479.jpg"  >}}



在本例中，钱包地址为 0xBd33c28a2a73dB44aca5a9937f0E5c4f7133926A，这就是以太坊钱包的地址。在密码学上，它是由私钥计算产生公钥并经过一些算法处理得出。这个信息是可以公开的。

在以太坊的交互和操作中，会经常用到这个地址。比如当你朋友转账给你，就需要填写你的钱包地址。你可以简单的将其类比为银行卡号。



### :four:**获取钱包私钥（可选）** {#get_private_key}

**钱包的私钥只在极少数的情况下才会被用到，而且相当于银行账号的密码，这个信息是不可以公开的！！**
获取方法如下，点击钱包右上角 “...” 并且选择 “Account details”。

{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638161097645.jpg"  >}}


之后点击 “Export Private Key” 并输入密码，即可得到一串类似 7c91dcb23c4244a78...3b421dc710a310df 这样的字符串，这就是你钱包的私钥，可以简单类比为银行卡密码。

{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638161343357.jpg"  >}}

{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638161377183.jpg"  >}}





至此，你将拥有了一个以太坊钱包，并且了解如何获取钱包相关信息。

</br>

> **<center> <font style="font-size:23px">我们再来简单的回忆一下刚刚发生了什么</font></center>**
>  
> 首先，安装了 MetaMask 插件，设置了一个 MetaMask 密码。值得注意的是，这个密码并非你的钱包密码，只是打开 MetaMask 操作你钱包的密码。
> 
> 之后，我们使用 MetaMask 创建了一个新钱包，生成了一个 12 个单词组成的助记词。这个助记词将是你最重要的秘密，请勿泄露！
> 
> 最后，MetaMask 使用助记词默认生成了一个钱包。一个助记词可以创建无限个钱包，每个钱包将包含一个钱包地址（公钥）和一个私钥。钱包地址是可以公开的，但是私钥是不可以公开的。




{{< admonition type="question" title="FAQ" >}}

* **助记词和钱包是什么关系？**

    以太坊的助记词是由 12 个随机单词组成，基于这些助记词可以生成多个钱包的公钥和私钥对。每个公钥私钥都是一个钱包。因此，如果助记词丢失，全部钱包将会丢失。如果单个钱包私钥泄密，仅仅对应钱包受到影响。
    
{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638167507066.jpg" caption="mnemonic and wallet" >}}

* **助记词和MetaMask是什么关系？**

    助记词的生成规则是有协议和标准的，最新的以太坊网络使用了 BIP39 标准来生成助记词，而 MetaMask 也支持 BIP39 标准。因此 MetaMask 就可以基于 BIP39 生成以太坊的助记词和钱包。想亲自体验一下 BIP39？可以查看 {{< link "https://iancoleman.io/bip39/" "https://iancoleman.io/bip39/" >}}
    
</br>

* **助记词随机产生，我可以创建无数个钱包，会不会有人生成了跟我一样的助记词？**

    这种情况在理论上是存在的，但实际中发生的概率非常小。下图为你猜中一个具体的 256 位私钥的难度。
    
    {{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638172698755.jpg" caption="Diffculty of guessing"  >}}

    
{{< /admonition >}}


## Step2: 了解必要的 Web3 安全知识 {#step_two}

**<center style="font-size:18px;">在获取 ETH 之前，有必要重点学习了解一下 Web3 的基础安全知识以免遭受经济损失。</center>**

### 常见 Web3 产生损失的案例{#web3_lose_example}

{{< admonition type="tips" title="tips" >}} 

* **钱包助记词、私钥泄漏或者丢失**

    Web3 由于是去中心化的，因此并不会像 Web2 的产品和服务一样拥有密码找回、账号风险锁定等功能。因此，你必须十分妥善的保管好助记词和私钥。一旦泄漏或者丢失，钱包内的资产将极难找回！
    
    **常见泄漏或者丢失的情况：**
    * 主动告诉别人（包括你的配偶），由对方无意泄漏或恶意攻击。
    * 写在纸上被别人发现或者纸张遗失、字迹模糊无法辨别。
    * 电脑或者手机中毒，在使用钱包时被窃取。
    * 电脑手机设备损坏，没有及时备份。
    * 复制粘贴助记词、密钥，导致被恶意软件或者输入法上传云端。
    * 自己设置了特殊的助记词、密钥记忆规则，结果忘记了规则。
    
    **知名案例**：
    
    * 预估有 300 万个比特币由于种种原因丢失。（{{< link "https://www.bankrate.com/investing/how-to-recover-lost-bitcoins-and-other-crypto/#:~:text=A%202017%20report%20from%20Chainalysis,when%20Bitcoin%20is%20fully%20mined." "数据来源">}})
    * 区块链被黑损失总金额约 260 亿美元。（{{< link "https://hacked.slowmist.io/" "数据来源" >}}）
    
    **应对方式：**
    
    * 使用专业密码管理器管理助记词和私钥。上手成本较低，安全性较好，全平台同步，适用于新手。推荐 [Bitwarden](https://bitwarden.com/) 或 [1Password](https://1password.com/zh-cn/)。

    * 保存、转发助记词或者密钥时，采用分段复制 + 手动输入 + 断网等多种方式，降低整体泄漏风险。
    * 使用硬件钱包。适用于有大量资产、丰富加密货币经验的用户。
    * 保持良好的电脑和手机使用习惯，不安装来历不明的软件。
    
    ---

* **钓鱼攻击**

    钓鱼攻击是最常见的攻击方式。它的原理是通过伪造官方网站、协议或者攻击官方账号发布虚假信息的方式引诱你上钩，然后让你转账或者购买虚假 NFT 等。

    **常见的攻击方式有：**
    
    * 任何区块链的交易、操作均需你签署消息来执行协议。攻击者会替换或者伪造协议让你签署（Sign Message），然而实际协议内容是恶意转账等。（像下面这种不明签名 Message 的就需要十分小心） 
        {{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638237733578.jpg" caption="Signature" >}} 
    * 钓鱼邮件攻击，你将可能收到伪造的邮件，里面的链接为钓鱼网站链接让你购买或者转账。
    * 在交易平台（例如：OpenSea）上，攻击者创建相似名称或者头像的项目，让你误解然后购买。
    * 通过搜索从第三方网站下载钱包插件，攻击者会替换或者插入恶意代码。
    * 社交媒体钓鱼攻击。“热心”群友可能会假装好心的发给你“官方网站”，或者发给你被修改过的插件、手机 APPs 等帮你安装，实际上则是经过修改的恶意软件。
    * Discord 上的 DM（私信） 钓鱼攻击。攻击者会仿造项目的官网账号，给你私信有关项目的白名单、抽奖或 mint 的信息，通常会附有钓鱼网站链接。
    
    **知名案例：**
    * OpenSea 上面 170 万美元的 NFT 被钓鱼攻击窃取。[（新闻链接）](https://www.theverge.com/2022/2/20/22943228/opensea-phishing-hack-smart-contract-bug-stolen-nft)
    * 周杰伦的 300 多万人民币的 NFT 在愚人节被钓鱼盗走。
    
    **其他案例：**
    * Discord 上的 DM（私信） 钓鱼攻击：[案例链接](https://mirror.xyz/yofine.eth/lHk2R_wawgmWltAirWWLgLlWIO4RzV7o_Qipf9C4z08)
    
    **应对方式：**
    * 永远警惕任何人发给你的任何消息。即便是好朋友或者官方渠道（因为他们的账户可能被黑掉了）。
    * 如无必要，请减少操作，不要随便乱点。操作越多，越容易产生风险。
    * 分散资产，规划钱包用途。就像你的银行卡一样，储蓄账户减少操作，仅作存储和转账不操作和使用。日常高频使用账户仅放少量资产。
    * 在刷空投或者参与新项目时，最好咨询有经验的朋友或者查看合约代码等以确保没有问题再进行操作。

---
* **浏览器插件攻击**

    通常我们会安装一些浏览器扩展或者插件来增强浏览体验。实际上浏览器插件具备的权限比较高，可以修改网页代码替换、篡改正常的合约，从而实现钓鱼攻击。

    **知名案例：**
    * 暂无实际案例，仅仅理论上具备这种风险和可能性

    
    **应对方式：**
    * 新开一个 Chrome Profile 仅仅安装 MetaMask 插件，不安装其他插件。需要使用钱包交互的时候，使用这个 Chrome Profile 进行操作。更多关于 Chrome Profile 的用法，请参考：[Share Chrome with others](https://support.google.com/chrome/answer/2364824)

    

{{< /admonition >}}

### 两点通用的安全建议 {#security_advice}

**分散资产，规划好钱包用途**
* 日常高频使用的热钱包存放少量资产，不同的钱包使用不同的助记词创建。
* 设置冷钱包，用于储蓄和转账，减少交互和使用。
* 周期性的创建新钱包进行资产转移。


很多资深人士的做法是将资产放入硬件钱包，每次需要对外交互的时候，都重新创建一个新钱包，用完即废弃。
{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638264390070.jpg" caption="Cold Wallet Operation" >}}


**警惕任何看到的消息**

* 学会正确的寻找官方网站、公告、项目等，不从第三方网站下载安装软件
* 看到信息需要多加思考。即便是官方发布的消息，有时候也可能是因为官方账号被黑导致的

{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638265952900.jpg" caption="Make sure" >}}


**<center>再次提醒：由于 Web3 分布式和匿名的特性，一旦资产被盗，将很难追回，请务必注意安全问题！
如果想要了解更多安全知识，推荐 [区块链黑暗森林自救手册](https://github.com/slowmist/Blockchain-dark-forest-selfguard-handbook)、[Web3 生存指南之防骗反诈安全手册V1.0](https://e7qjl676i8.feishu.cn/docs/doccn2rvEMHefBYKvyTVRGwe7Pf)。</center>**




## Step3: 获取 ETH {#step3}

**<center>
我们将按照你的个人情况为你推荐几种获取 ETH 的方式 </br>
（风险提示：请遵循当地法律法规进行操作。）</center>**


## Step4: 链接钱包 {#step4}

**<center>刚刚发生了什么？什么是链接钱包？</center>**


{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638268812790.jpg" caption="Connect Wallet" >}}


### **链接钱包是 Web3 应用的重要特征** {#Web3_important_features}

* 当你看到网站上有“Connect Wallet”按钮时，就说明这是一个 Web3 APP
* 链接钱包相当于登录网站 APP，登录之后，网站将有权限读取你的钱包信息以及区块链上你的资产
* 由于区块链是公开透明的，网站将可以读取你的全部在区块链上的信息
* 你的钱包就是你的账户，而且是匿名的，你无需填写密码或者任何联系信息

</br>   

## Step5: 铸造我的第一个 NFT {#create_my_first_nft}

**终于，万事俱备，你可以开始铸造第一个 NFT 了。**

### :one: **制作你想要铸造的 NFT 头像并且铸造** {#make_you_nft}

本 NFT 为简笔画风格头像类 NFT，支持按照你的个人喜好在线制作。免费不限量铸造。原始图片素材来自于【国产良心 NFT】项目（为什么可以用？）


{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638274893368.jpg" caption="Cast NFT" >}}



### :two: **在 MetaMask 确认价格并进行铸造** {#why_gas_fee}

**不是说免费吗？为什么还要钱？**

任何在区块链网络中的操作，都需要支付一笔手续费（Gas Fee）给区块链矿工来维持去中心化网络运行。

本 NFT 项目为免费项目，但是铸造的过程是需要网络矿工帮你完成，因此你需要支付一笔

值得注意的是，Gas Fee 是浮动的你可以看到 MetaMask 的 Gas Fee 部分在不断刷新。这是为什么呢？

这是因为每一个区块的容量都是有限的，不是所有交易都会被及时打包完成。越高的 Gas Fee 越容易让矿工优先打包你的交易，完成的速度越快。

建议你可以选择等到 Gas Fee 低一些的时候再铸造，这样可以节约一些钱。你可以在 [https://etherscan.io/gastracker](https://etherscan.io/gastracker) 看到当前的 Gas Fee。建议等到 15 - 40 Gwei 的区间进行铸造。


{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638275978097.jpg" caption="Pay Gas Fee" >}}

### :three: **查看铸造结果并使用、分享我的第一个 NFT！** {#my_nft}

<center>:point_down: :point_down: :point_down: :point_down: :point_down:</center>
{{< image src="https://blog.cdn.scshark.com/scshark/2022-09-22/16638278283168.jpg" caption="Jack wechat avatar NFT" >}}


</br>

**<center style="color:#525f6c;font-size:17px;">Enjoy your Web3 and NFT journey!</center>**

</br>

**<center >- END -</center>**

</br>