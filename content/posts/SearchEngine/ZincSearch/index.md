---
title: "ZincSearch"
date: 2023-03-07T17:08:18+08:00
lastmod: 2023-03-07T17:08:18+08:00
draft: false
authors: ["scshark"]
description: "ZincSearch 是一个进行全文索引的搜索引擎。它是 Elasticsearch 的轻量级替代品，并且使用一小部分资源运行。"
tags: ["golang","search","search engine","elasticsearch","logs"]
categories: ["golang"]
series: []

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: ""
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2023-03-07/16781802124848.jpg"

toc:
  enable: true
math:
  enable: false
lightgallery: true
license: ""

---

<!--more-->

# 欢迎来到 ZincSearch

### 为什么选择 ZincSearch {#why_zinc}

ZincSearch 是因为没有可用的搜索引擎可以满足我的需求而启动的。

虽然 Elasticsearch 是一个非常好的产品，但它很复杂并且需要大量资源并且已经有十多年的历史了。我构建了 ZincSearch，因此人们无需做大量工作就可以更轻松地使用全文搜索索引。

### 安装 {#install_zinc}

当您第一次启动 ZincSearch 时，您需要 `ZINC_FIRST_ADMIN_USER` 和 `ZINC_FIRST_ADMIN_PASSWORD` 环境变量。在后续的 ZincSearch 运行中不需要它们。

#### MacOS - Homebrew {#install_in_mac}

```
brew tap zinclabs/tap
brew install zinclabs/tap/zinc
mkdir data
ZINC_FIRST_ADMIN_USER=admin ZINC_FIRST_ADMIN_PASSWORD=Complexpass#123 zincsearch
```

现在将浏览器指向{{< link "http://localhost:4080" "http://localhost:4080">}}并登录

#### MacOS/Linux Binaries {#install_in_linux}

二进制文件可以从相应平台的{{< link "https://github.com/zinclabs/zincsearch/releases" "发布页面下载">}}。

创建将存储数据的数据文件夹
```
mkdir data
ZINC_FIRST_ADMIN_USER=admin ZINC_FIRST_ADMIN_PASSWORD=Complexpass#123 ./zincsearch

```

现在将浏览器指向{{< link "http://localhost:4080" "http://localhost:4080">}}并登录


#### Docker {#install_in_docker}

Docker image 可在 {{< link "https://gallery.ecr.aws/zinclabs/zinc" "https://gallery.ecr.aws/zinclabs/zinc">}} 获得

```
mkdir data
docker run -v /full/path/of/data:/data -e ZINC_DATA_PATH="/data" -p 4080:4080 \
    -e ZINC_FIRST_ADMIN_USER=admin -e ZINC_FIRST_ADMIN_PASSWORD=Complexpass#123 \
    --name zincsearch public.ecr.aws/zinclabs/zinc:latest

```

现在将浏览器指向{{< link "http://localhost:4080" "http://localhost:4080">}}并登录

**有错误？**

Error - 1 : 如果您安装了 AWS CLI 并收到登录错误，请运行以下命令：

```
aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws

```

Error - 2：如果在运行 docker 命令时出现权限被拒绝的错误，那么您可能需要修复数据文件夹的权限。docker 中的 zincsearch 可执行文件以非 root 用户身份运行，并且需要权限才能写入数据文件夹。您可以运行以下命令来修复它：

```
chmod a+rwx ./data

```

### 环境变量 {#environment-variables}


| Environment Variable	     | Default Value	 | Mandatory	 | Description                                                |
|---------------------------|----------------|------------|:-----------------------------------------------------------|
| ZINC_FIRST_ADMIN_USER     | -              | 第一次运行  | ZincSearch 的第一个管理员用户。首次运行 ZincSearch 后不需要。 |
| ZINC_FIRST_ADMIN_PASSWORD | -              | 第一次运行  | 第一个管理员用户的密码                                      |
| ZINC_SERVER_ADDRESS	      | 0.0.0.0        | no         | 要绑定的 ZincSearch 服务器 IP 地址                         |
| ZINC_SERVER_PORT          | 4080           | no         | ZincSearch 服务器监听 http 端口
                           |
| GIN_MODE                  | -              | no         | 如果值为 release 则 gin 将在生产模式下运行。                |
| ZINC_DATA_PATH	           | ./data         | no         | 如果未提供，则默认为当前工作目录中的“data”文件夹。           |
| ZINC_ICE_COMPRESSOR       | zstd           | no         | 压缩段文件的算法，默认为：zstd，支持：snappy, s2, zstd         |


### Quickstart {#Quickstart}

Check {{< link "https://docs.zinc.dev/quickstart/" "Quickstart">}}



---
<center > - END - </center>