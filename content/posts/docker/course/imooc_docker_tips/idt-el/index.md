---
title: "Docker入门介绍"
date: 2023-03-28T18:28:57+08:00
lastmod: 2023-03-28T18:28:57+08:00
draft: false
authors: ["scshark"]
description: "容器正在引领基础架构的一场新的革命"
tags: ["Docker","Container"]
categories: ["Docker"]
series: ["imooc-docker-tips"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "https://blog.cdn.scshark.com/scshark/2023-03-30/16801541897374.jpg"
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2023-03-30/16801541897374.jpg"

toc:
  enable: true
math:
  enable: false
lightgallery: true
license: ""

---

<!--more-->

# 容器技术的介绍 {#contain-introduce}

{{< admonition type="note" >}}

注意我们这里所说的容器**container**是指的一种技术，而**Docker**只是一个容器技术的实现，或者说让容器技术普及开来的最成功的实现

{{< /admonition >}}

## 容器正在引领基础架构的一场新的革命 {#container-leading-revolution}

- 90年代的PC
- 00年代的虚拟化
- 10年代的cloud
- 11年代的container

## 什么是container(容器）？{#what-container}

容器是一种快速的打包技术

Package Software into Standardized Units for Development, Shipment and Deployment

> 将软件打包成标准化的单元，以便进行开发、装运和部署。

- 标准化
- 轻量级
- 易移植

## 为什么容器技术会出现？ {#why-container-appear}

容器出现之前 ：

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-30/16801558053825.jpg" caption="地狱矩阵！！" >}}


容器技术出现之后


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-30/16801559989453.jpg" caption="Container" >}}


容器 vs 虚拟机 

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-30/16801560426753.jpg" caption="容器 vs 虚拟机"  >}}

Linux Container容器技术的诞生于2008年（Docker诞生于2013年），解决了IT世界里“集装箱运输”的问题。**Linux Container（简称LXC）** 它是一种内核轻量级的操作系统层虚拟化技术。Linux Container主要由 **Namespace** 和 **Cgroups**  两大机制来保证实现

- **Namespace** 命名空间主要用于资源的隔离（诞生于2002年）
- **Cgroups(Control Groups)** 就负责资源管理控制作用，比如进程组使用CPU/MEM的限制，进程组的优先级控制，进程组的挂起和恢复等等。（由Google贡献，2008年合并到了Linux Kernel）

## 容器的标准化 {#container-standardization}

**`docker != container`**

在2015年，由Google、Docker、红帽等厂商联合发起了**OCI（Open Container Initiative）** 组织，致力于容器技术的标准化 

### 容器运行时标准 （runtime spec）{#container-runtime-spec}

简单来讲就是规定了容器的基本操作规范，比如如何下载镜像，创建容器，启动容器等。

### 容器镜像标准（image spec）{#container-image-spec}

主要定义镜像的基本格式。

## 容器是关乎“速度” {#container-speed}

- 容器会加速你的软件开发
- 容器会加速你的程序编译和构建
- 容器会加速你的测试
- 容器会加速你的部署
- 容器会加速你的更新
- 容器会加速你的故障恢复


## 容器的快速发展和普及 {#container-develop}

{{< admonition type="note" >}}

到2020年，全球超过50%的公司将在生产环境中使用container —— Gartner 5


{{< /admonition >}}

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-30/16801571140521.jpg" caption="协作应用开发平台对于开发人员至关重要" >}}



## Docker 的架构 {#docker-framework}


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-30/16801576892668.jpg" caption="Docker基本架构" >}}


---
<center > - END - </center>




