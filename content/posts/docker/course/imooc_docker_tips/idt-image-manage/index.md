---
title: "镜像的创建管理和发布"
date: 2023-03-30T16:27:52+08:00
lastmod: 2023-03-30T16:27:52+08:00
draft: false
authors: ["scshark"]
description: "Docker 系统性入门+进阶实践 </br> 讲师：<a href='https://github.com/xiaopeng163' _target='blank' >Peng Xiao</a>"
tags: ["Docker","Dockerfile","Buildkit"]
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

## 镜像的获取 {#image-pull}

- **pull from registry (online**) 从**registry**拉取
    - **public**（公有）
    - **private**（私有）
- **build from Dockerfile (online)** 从**Dockerfile**构建
- **load from file (offline)** 文件导入 （离线）


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-30/16801664206170.jpg" caption="获取镜像的三种方式">}}

## 镜像的基本操作 {#image-operation}

```shell
$ docker image

Usage:  docker image COMMAND

Manage images

Commands:
build       Build an image from a Dockerfile
history     Show the history of an image
import      Import the contents from a tarball to create a filesystem image
inspect     Display detailed information on one or more images
load        Load an image from a tar archive or STDIN
ls          List images
prune       Remove unused images
pull        Pull an image or a repository from a registry
push        Push an image or a repository to a registry
rm          Remove one or more images
save        Save one or more images to a tar archive (streamed to STDOUT by default)
tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE

Run 'docker image COMMAND --help' for more information on a command.
```

### 镜像的拉取Pull Image{#pull-image}


默认从**Docker Hub**拉取，如果不指定版本，会拉取最新版

```shell
$ docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
·
·
·
Digest: sha256:df13abe416e37eb3db4722840dd479b00ba193ac6606e7902331dcea50f4f1f2
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```

指定版本

```shell
$ docker pull nginx:1.20.0
1.20.0: Pulling from library/nginx
69692152171a: Already exists
·
·
·
Digest: sha256:ea4560b87ff03479670d15df426f7d02e30cb6340dcd3004cdfc048d6a1d54b4
Status: Downloaded newer image for nginx:1.20.0
docker.io/library/nginx:1.20.0
```

从**Quay**上拉取镜像

```shell
$ docker pull quay.io/bitnami/nginx
Using default tag: latest
latest: Pulling from bitnami/nginx
·
·
·
Digest: sha256:d143befa04e503472603190da62db157383797d281fb04e6a72c85b48e0b3239
Status: Downloaded newer image for quay.io/bitnami/nginx:latest
quay.io/bitnami/nginx:latest
```


### 镜像的查看 {#image-ls}

```shell
$ docker image ls
REPOSITORY              TAG       IMAGE ID       CREATED       SIZE
quay.io/bitnami/nginx   latest    0922eabe1625   6 hours ago   89.3MB
nginx                   1.20.0    7ab27dbbfbdf   10 days ago   133MB
nginx                   latest    f0b8a9a54136   10 days ago   133MB
```

### 镜像的删除 {#image-rm}
```shell
$ docker image rm 0922eabe1625
Untagged: quay.io/bitnami/nginx:latest
Untagged: quay.io/bitnami/nginx@sha256:d143befa04e503472603190da62db157383797d281fb04e6a72c85b48e0b3239
Deleted: sha256:0922eabe16250e2f4711146e31b7aac0e547f52daa6cf01c9d00cf64d49c68c8
Deleted: sha256:5eee4ed0f6b242e2c6e4f4066c7aca26bf9b3b021b511b56a0dadd52610606bd
·
·
·
```

## 镜像的导出和导入 (offline) {#image-offline}

导入：**`docker image load -i .\nginx.image`**

导出：**`docker image save nginx:1.20.0 -o nginx.image`**


## Dockerfile 介绍 {#dockerfile-reference}

Docker can build images automatically by reading the instructions from a Dockerfile. A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image. Using docker build users can create an automated build that executes several command-line instructions in succession.

> 翻译：**Docker** 可以通过读取**Dockerfile**中的指令自动构建镜像。 **Dockerfile**是一个文本文件，包含用户可以在命令行上调用以组装图像的所有命令。使用 **`docker build`** ，用户可以创建一个自动化构建，按顺序执行多个命令行指令。

- **Dockerfile**是用于构建**docker**镜像的文件
- **Dockerfile**里包含了构建镜像所需的“指令” 
- **Dockerfile**有其特定的语法规则

### 举例：执行一个Python程序{#dockerfile-example}

容器及进程，所以镜像就是一个运行这个进程所需要的环境。

假如我们要在一台ubuntu 21.04上运行下面这个hello.py的Python程序

hello.py的文件内容：
```python
print("hello docker")
```

第一步：准备Python环境

```shell
apt-get update && \
DEBIAN_FRONTEND=noninteractive apt-get install --no-install-recommends -y python3.9 python3-pip python3.9-dev
```

第二步：运行hello.py

```shell
$ python3 hello.py
hello docker
```

如果结合Dockerfile就是这样

### 一个Dockerfile的基本结构 {#dockerfile-struct}

Dockerfile
```dockerfile
FROM ubuntu:20.04
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install --no-install-recommends -y python3.9 python3-pip python3.9-dev
ADD hello.py /
CMD ["python3", "/hello.py"]
```

## 镜像的构建和分享 {#image-build-push}

dockerfile构建image：**`docker image build -t scshark/hello:1.0 .`** ,`-t` 命名image名字

创建一个引用 SOURCE_IMAGE 的标记 TARGET_IMAGE：**`docker image tag hello scshark/hello:1.0`**

登录docker hub ：**`docker login`**

push 到docker hub ： **`docker push scshark/hello:1.0`**

docker hub pull image：**`docker pull scshark/hello:1.0`**

## 通过 commit 创建镜像 {#docker-commit}

将容器另存为镜像 

**`docker container commit <container name or id> <new image name>`**


## 关于 scratch 镜像 {#about-scratch}

**Scratch** 是一个空的Docker镜像。

通过scratch来构建一个基础镜像。

`hello.c`

```c
#include <stdio.h>
int main()
{
    printf("hello docker\n");
}
```

编译成一个二进制文件

```shell
$ gcc --static -o hello hello.c
$ ./hello
hello docker
$
```

然后 **`Dockerfile`**

```Dockerfile
FROM scratch
ADD hello /
CMD ["/hello"]
```

构建

```shell
$ docker build -t hello .
$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
hello        latest    2936e77a9daa   40 minutes ago   872kB

```

运行

```
$ docker container run -it hello
hello docker

```

## Buildkit {#Buildkit}


Buildkit这个feature能够提高build的效率。

[https://docs.docker.com/develop/develop-images/build_enhancements/](https://docs.docker.com/develop/develop-images/build_enhancements/)

### Windows {#Windows}

查看是否激活了Buildkit

{{< link src="https://blog.cdn.scshark.com/scshark/2023-03-30/16801704333446.jpg" >}}


如果没有，请添加
```
"features": {
  "buildkit": true
}
```

然后 **`Apply`** & **`Restart`** 

### Mac {#Mac}

查看是否激活了Buildkit

{{< link src="https://blog.cdn.scshark.com/scshark/2023-03-30/16801707058785.jpg" >}}


如果没有，请添加

```
"features": {
  "buildkit": true
}
```

### Linux {#Linux}

在 **`/etc/docker/daemon.json`** 里添加（如果没有这个文件，则新建）, 然后重启docker

```
{ "features": { "buildkit": true } }
```


或者在执行docker build命令时设置


```
$ DOCKER_BUILDKIT=1 docker build .

```




---
<center > - END - </center>
