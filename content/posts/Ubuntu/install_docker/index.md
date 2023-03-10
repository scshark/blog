---
title: "Ubuntu 安装Docker"
date: 2023-03-10T13:26:11+08:00
lastmod: 2023-03-10T13:26:11+08:00
draft: false
authors: ["scshark"]
description: "Docker 是一个开源的应用容器引擎，基于 Golang 语言开发，可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 服务器。"
tags: ["Ubuntu","Docker"]
categories: ["Ubuntu"]
series: []

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "https://blog.cdn.scshark.com/scshark/2023-03-10/16784329568566.jpg"
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2023-03-10/16784329568566.jpg"

toc:
  enable: true
math:
  enable: false
lightgallery: true
license: ""

---


<!--more-->

## Docker是什么？{#what_docker}

　　Docker 是一个开源的应用容器引擎，基于 Golang 语言开发，可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 服务器。容器是一个沙箱机制，相互之间不会有影响（类似于我们手机上运行的 app），并且容器开销是很低的。

　　Docker 是一个供开发人员和系统管理员构建、运行和与容器共享应用程序的平台。使用容器部署应用程序称为容器化。容器并不是新事物，但它们用于轻松部署应用程序却是新鲜的。
　　
> 注意：Docker并非是一个通用的容器工具，它依赖于已存在并运行的Linux内核环境。


## Docker的优势有哪些？{#docker-advantage}

- 灵活性：即使是最复杂的应用程序也可以容器化。
- 轻量级：容器利用并共享主机内核，使它们在系统资源方面比虚拟机更有效率。
- 可移植：您可以在本地构建，部署到云上，并在任何地方运行。
- 松耦合：容器是高度自给自足和封装的，允许您在不影响其他容器的情况下替换或升级其中一个。
- 可扩展：您可以跨数据中心增加和自动分发容器副本。
- 安全性：容器对进程应用主动约束和隔离，而不需要用户进行任何配置。

## Docker的作用？{#docker-effect}

　　Docker 是一个用于开发、传送和运行应用程序的开放平台。Docker 使您能够将应用程序与基础设施分开，以便您可以快速交付软件。使用 Docker，您可以像管理应用程序一样管理基础设施。通过利用 Docker 的快速交付、测试和部署代码的方法，您可以显著减少编写代码和在生产中运行代码之间的延迟。Docker(opens new window)是个划时代的开源项目，它彻底释放了计算虚拟化的威力，极大提高了应用的维护效率，降低了云计算应用开发的成本！使用 Docker，可以让应用的部署、测试和分发都变得前所未有的高效和轻松！**无论是应用开发者、运维人员、还是其他信息技术从业人员，都有必要认识和掌握 Docker，节约有限的生命。**
　　
　　
## 查看系统发行版本 {#system_version}

```
cat /proc/version
```
{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-10/16784334590443.jpg" >}}


## 安装Docker的三种方式 {#install_docker}


- 1、设置Docker的存储库并从中安装，以便于安装和升级任务（推荐使用，本文主要是以这种方式进行Docker安装教学）。
- 2、下载DEB 包并 手动安装，完全手动管理升级。这在诸如在无法访问Internet的系统上安装 Docker 等情况下很有用。
- 3、在测试和开发环境中，一些用户选择使用自动化 便利脚本来安装 Docker。

### 使用存储库安装Docker【推荐使用】{#apt-install}

> 在新主机上首次安装 Docker Engine 之前，您需要设置 Docker 存储库。之后，您可以从存储库安装和更新 Docker。

#### 设置存储库 {#set_rep}

> 注意：如果无法更新，可能是你的软件源指向是国外的服务器，很可能已经被墙。

1、更新apt包索引：

```
sudo apt-get update
```

2、安装必备的软件包以允许apt通过 HTTPS 使用存储库（repository）：

```
sudo apt-get install ca-certificates curl gnupg lsb-release
```

3、添加Docker官方版本库的GPG密钥：

```
sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

4、使用以下命令设置存储库：

```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### 安装Docker{#install_docker_apt}

**更新apt包索引，安装最新版本的Docker Engine、containerd 和 Docker Compose：**

```
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

```

 使用Docker官方的源你很有可能和我一样很慢很慢一直卡在不动【那么你可以切切换成阿里云镜像源看看】
 
---
 
### 切换阿里云镜像源安装Docker {#ali_install_docker}
 
1、卸载可能存在的或者为安装成功的Docker版本
 
 ```
 sudo apt-get remove docker docker-engine docker-ce docker.io
 
 ```
2、添加阿里云的GPG密钥
 
 ```
 curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
 ```
 
3、使用以下命令设置存储库
 
 ```
 sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

4、安装最新版本的Docker(飞速安装)

```
sudo apt-get update
 
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

5、验证Docker是否安装成功

```
-- 查看docker 版本
docker version
```

### 启动Docker {#start_docker}

1、安装完成后，运行如下命令验证 Docker 服务是否在运行

```
systemctl status docker
```

2、运行以下命令启动Docker服务
 
```
sudo systemctl start docker
```

3、设置Docker服务在每次开机时自动启动

```
sudo systemctl enable docker

```

4、查看docker运行状态

```
systemctl status docker
```

### 验证Docker是否运行正常 {#docker_status}

> 注意：执行下面的命令会下载一个Docker测试镜像，并在容器中执行一个“hello-world”样例程序。

```
sudo docker run hello-world
```

如果你看到类似下方的输出，那么祝贺你，Docker能够正常运行在你的Ubuntu系统中了。


```
ubuntu@VM-16-10-ubuntu:~$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:53f1bbee2f52c39e41682ee1d388285290c5c8a76cc92b42687eecf38e0af3f0
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

---
<center > - END - </center>