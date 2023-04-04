---
title: "容器快速上手"
date: 2023-03-30T14:35:25+08:00
lastmod: 2023-03-30T14:35:25+08:00
draft: false
authors: ["scshark"]
description: "Docker 系统性入门"
tags: ["Docker","Container","VM"]
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


## Docker CLI 命令行介绍 {#docker-cli}

`Docker Version` --查看**docker**版本信息


```
$ docker version
Client: Docker Engine - Community
Version:           20.10.0
API version:       1.41
Go version:        go1.13.15
Git commit:        7287ab3
Built:             Tue Dec  8 18:59:40 2020
OS/Arch:           linux/amd64
Context:           default
Experimental:      true

Server: Docker Engine - Community
Engine:
Version:          20.10.0
API version:      1.41 (minimum version 1.12)
Go version:       go1.13.15
Git commit:       eeddea2
Built:            Tue Dec  8 18:57:45 2020
OS/Arch:          linux/amd64
Experimental:     false
containerd:
Version:          1.4.3
GitCommit:        269548fa27e0089a8b8278fc4fc781d7f65a939b
runc:
Version:          1.0.0-rc92
GitCommit:        ff819c7e9184c13b7c2607fe6c30ae19403a7aff
docker-init:
Version:          0.19.0
GitCommit:        de40ad0

```

## docker命令行的基本使用 {#docker-cli-usage}


**docker + 管理的对象（比如容器，镜像） + 具体操作（比如创建，启动，停止，删除）**

例如：

**`docker image pull nginx`** 拉取一个叫nginx的docker image镜像

**`docker container stop web`** 停止一个叫web的docker container容器

## Image vs Container 镜像 vs 容器 {#image-container}

### image镜像 {#ic-image}

- **Docker image**是一个 **`read-only`** 文件
- 这个文件包含文件系统，源码，库文件，依赖，工具等一些运行**application**所需要的文件
- 可以理解成一个模板
- **Docker image**具有分层的概念

### container容器 {#ic-container}

- “一个运行中的**docker image**”
- 实质是复制**image**并在**image**最上层加上一层 **read-write** 的层 （称之为 **container layer** ,容器层）
- 基于同一个**image**可以创建多个**container**


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-30/16801591331056.jpg" >}}

### docker image的获取 {#get-docker-image}

- 自己制作
- 从**registry**拉取（比如docker hub）


## 容器的基本操作 {#container-basic-operation}


| 操作                 | 命令（全）                                     | 命令（简）                           |
|----------------------|----------------------------------------------|------------------------------------|
| 容器创建             | docker container run \<image name\>            | docker run \<image name\>            |
| 容器的列出（up）       | docker container ls                          | docker ps                          |
| 容器的列出（up和exit） | docker container ls -a                       | docker ps -a                       |
| 容器的停止           | docker container stop \<container name or id\> | docker stop \<container name or id\> |
| 容器的删除           | docker container rm \<container name or id\>   | docker rm \<container name or id\>   |


例如这样：

```shell

$ docker container run nginx
$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
343fd4031609   nginx     "/docker-entrypoint.…"   6 seconds ago   Up 5 seconds   80/tcp    xenodochial_clarke
$ docker container ls
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
343fd4031609   nginx     "/docker-entrypoint.…"   14 seconds ago   Up 13 seconds   80/tcp    xenodochial_clarke
$ docker container stop 34
34
$ docker container ls -a
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                      PORTS     NAMES
343fd4031609   nginx     "/docker-entrypoint.…"   29 seconds ago   Exited (0) 5 seconds ago              xenodochial_clarke
d9095daa8bcf   nginx     "/docker-entrypoint.…"   28 minutes ago   Exited (0) 28 minutes ago             suspicious_shamir
$ docker container rm 34
34
$ docker container ls -a
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                      PORTS     NAMES
d9095daa8bcf   nginx     "/docker-entrypoint.…"   28 minutes ago   Exited (0) 28 minutes ago             suspicious_shamir
$

```

## docker container 命令小技巧 {#container-cli-tips}

### 批量停止 {#batch-stop}

```shell
$ docker container ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
cd3a825fedeb   nginx     "/docker-entrypoint.…"   7 seconds ago    Up 6 seconds    80/tcp    mystifying_leakey
269494fe89fa   nginx     "/docker-entrypoint.…"   9 seconds ago    Up 8 seconds    80/tcp    funny_gauss
34b68af9deef   nginx     "/docker-entrypoint.…"   12 seconds ago   Up 10 seconds   80/tcp    interesting_mahavira
7513949674fc   nginx     "/docker-entrypoint.…"   13 seconds ago   Up 12 seconds   80/tcp    kind_nobel
```

方法一：

```shell
$ docker container stop cd3 269 34b 751
```

方法二：
```shell
$ docker container stop $(docker container ps -aq)
cd3a825fedeb
269494fe89fa
34b68af9deef
7513949674fc
$ docker container ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                     PORTS     NAMES
cd3a825fedeb   nginx     "/docker-entrypoint.…"   30 seconds ago   Exited (0) 2 seconds ago             mystifying_leakey
269494fe89fa   nginx     "/docker-entrypoint.…"   32 seconds ago   Exited (0) 2 seconds ago             funny_gauss
34b68af9deef   nginx     "/docker-entrypoint.…"   35 seconds ago   Exited (0) 2 seconds ago             interesting_mahavira
7513949674fc   nginx     "/docker-entrypoint.…"   36 seconds ago   Exited (0) 2 seconds ago             kind_nobel
$
```

> `-a` 列出所有容器
> 
> `-q` 仅显示容器 ID


### 批量删除 {#batch-delete}

和批量停止类似，可以使用 **`docker container rm $(docker container ps -aq)`**

{{< admonition type="note" >}}

**`docker system prune -a -f`** 可以快速对系统进行清理，删除停止的容器，不用的image，等等



{{< /admonition >}}


## Container Mode 容器运行的各种模式 {#container-mode}


### attach 模式 {#container-mode-attach}

范例指令: **`docker container run -p 80:80 nginx`**

- 透过这种方式创建容器的话，容器在前台执行
- 容器的输入输出结果会反映到本地端，本地端的输入输出也会反映到容器，例如能在终端机看到网页浏览器的 log，**`ctrl + c`** 会让容器停止执行
- 一般情况不推荐使用

### detach 模式 {#container-mode-attach}

范例指令: **`docker container run -d -p 80:80 nginx`**

- 容器会在后台执行

## 连接容器的 shell {#container-shell}

**`docker container run -it `** 创建一个容器并进入交互式模式

```shell
➜  ~ docker container run -it busybox sh
/ #
/ #
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var
/ # ps
PID   USER     TIME  COMMAND
    1 root      0:00 sh
    8 root      0:00 ps
/ # exit
```

**`docker container exec -it`** 在一个已经运行的容器里执行一个额外的**command**

```shell
➜  ~ docker container run -d nginx
33d2ee50cfc46b5ee0b290f6ad75d724551be50217f691e68d15722328f11ef6
➜  ~
➜  ~ docker container exec -it 33d sh
#
#
# ls
bin  boot  dev  docker-entrypoint.d  docker-entrypoint.sh  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
#
# exit
➜  ~
```

## 容器和虚拟机 Container vs VM {#container-vs-vm}



{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-30/16801631298991.jpg" caption="容器和虚拟机的区别">}}



### 容器不是Mini虚拟机 {#container-not-miniVM}

- 容器其实是进程 **`Containers are just processes`**
- 容器中的进程被限制了对CPU内存等资源的访问
- 当进程停止后，容器就退出了

### 容器的进程process {#container-process}


{{< admonition type="note" >}}

以下是在**Ubuntu20.04**中演示，因**Windows**环境下的**Docker**和**Linux**具有一些差异。**pstree** 命令需要额外安装，可以使用 **`yum install psmisc` 或者 `sudo apt-get install psmisc`** 安装

```shell
➜  ~ docker container run -d nginx
57fe4033dd7e1e620a0b6a7b83b85d4f8f98772f0ce585624c384de254826fd0
➜  ~ docker container ls
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
57fe4033dd7e   nginx     "/docker-entrypoint.…"   4 seconds ago   Up 3 seconds   80/tcp    festive_proskuriakova
➜  ~ docker container top 57f
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                7646                7625                0                   12:14               ?                   00:00:00            nginx: master process nginx -g daemon off;
systemd+            7718                7646                0                   12:14               ?                   00:00:00            nginx: worker process
➜  ~
➜  ~
➜  ~  ps -aef --forest
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 May14 ?        00:00:00 /init
root       157     1  0 May14 ?        00:00:00 /init
root       523   157  0 May14 ?        00:02:32  \_ /usr/bin/dockerd -p /var/run/docker.pid
root       545   523  0 May14 ?        00:25:55  |   \_ containerd --config /var/run/docker/containerd/containerd.toml --log-level info
root      7625   157  0 12:14 ?        00:00:00  \_ /usr/bin/containerd-shim-runc-v2 -namespace moby -id 57fe4033dd7e1e620a0b6a7b83b85d4f8f98772f0ce585624c384de254826fd0 -address /var/run/d
root      7646  7625  0 12:14 ?        00:00:00      \_ nginx: master process nginx -g daemon off;
systemd+  7718  7646  0 12:14 ?        00:00:00          \_ nginx: worker process
root      6442     1  0 May18 ?        00:00:00 /init
root      6443  6442  0 May18 ?        00:00:00  \_ /init
penxiao   6444  6443  0 May18 pts/2    00:00:01      \_ -zsh
penxiao   7770  6444  0 12:15 pts/2    00:00:00          \_ ps -aef --forest
➜  ~
➜  ~ pstree -halps 7718
init,1
└─init,157
    └─containerd-shim,7625 -namespace moby -id 57fe4033dd7e1e620a0b6a7b83b85d4f8f98772f0ce585624c384de254826fd0 -address /var/run/docker/containerd/containerd.sock
        └─nginx,7646
            └─nginx,7718
```
{{< /admonition >}}

## docker container run 背后发生了什么？ {#docker-container-run-what-happen}

**`$ docker container run -d --publish 80:80 --name webhost nginx`**


1. 在本地查找是否有**nginx**这个**image**镜像，但是没有发现
2. 去远程的**image registry**查找**nginx**镜像（默认的**registry**是**Docker Hub**)
3. 下载最新版本的**nginx**镜像 （ **nginx:latest** 默认)
4. 基于**nginx**镜像来创建一个新的容器，并且准备运行
5. **docker engine**分配给这个容器一个虚拟IP地址
6. 在宿主机上打开80端口并把容器的80端口转发到宿主机上
7. 启动容器，运行指定的命令（这里是一个**shell**脚本去启动**nginx**）


---
<center > - END - </center>
