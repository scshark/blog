---
title: "Dockerfile完全指南"
date: 2023-03-31T10:53:17+08:00
lastmod: 2023-03-31T10:53:17+08:00
draft: false
authors: ["scshark"]
description: "Docker 系统性入门+进阶实践 </br> 讲师：<a href='https://github.com/xiaopeng163' _target='blank' >Peng Xiao</a>"
tags: ["Docker","Dockerfile"]
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



## 基础镜像的选择 (FROM) {#image-select}

### 基本原则 {#basic-tenet}

- 官方镜像优于非官方的镜像，如果没有官方镜像，则尽量选择**Dockerfile**开源的
- 固定版本tag而不是每次都使用latest
- 尽量选择体积小的镜像

```
$ docker image ls
REPOSITORY      TAG             IMAGE ID       CREATED          SIZE
bitnami/nginx   1.18.0          dfe237636dde   28 minutes ago   89.3MB
nginx           1.21.0-alpine   a6eb2a334a9f   2 days ago       22.6MB
nginx           1.21.0          d1a364dc548d   2 days ago       133MB
```

### Build一个Nginx镜像 {#build-nginx-image}

假如我们有一个 `index.html` 文件

```html
<h1>Hello Docker</h1>
```

准备一个Dockerfile

```dockerfile
FROM nginx:1.21.0-alpine

ADD index.html /usr/share/nginx/html/index.html
```


## 通过 RUN 执行指令 {#instruct-run}

**RUN 主要用于在Image里执行指令，比如安装软件，下载文件等。**

```
$ apt-get update
$ apt-get install wget
$ wget https://github.com/ipinfo/cli/releases/download/ipinfo-2.0.1/ipinfo_2.0.1_linux_amd64.tar.gz
$ tar zxf ipinfo_2.0.1_linux_amd64.tar.gz
$ mv ipinfo_2.0.1_linux_amd64 /usr/bin/ipinfo
$ rm -rf ipinfo_2.0.1_linux_amd64.tar.gz
```

**Dockerfile**

```dockerfile
FROM ubuntu:20.04
RUN apt-get update
RUN apt-get install -y wget
RUN wget https://github.com/ipinfo/cli/releases/download/ipinfo-2.0.1/ipinfo_2.0.1_linux_amd64.tar.gz
RUN tar zxf ipinfo_2.0.1_linux_amd64.tar.gz
RUN mv ipinfo_2.0.1_linux_amd64 /usr/bin/ipinfo
RUN rm -rf ipinfo_2.0.1_linux_amd64.tar.gz
```

镜像的大小和分层

```
$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
ipinfo       latest    97bb429363fb   4 minutes ago   138MB
ubuntu       21.04     478aa0080b60   4 days ago      74.1MB
$ docker image history 97b
IMAGE          CREATED         CREATED BY                                      SIZE      COMMENT
97bb429363fb   4 minutes ago   RUN /bin/sh -c rm -rf ipinfo_2.0.1_linux_amd…   0B        buildkit.dockerfile.v0
<missing>      4 minutes ago   RUN /bin/sh -c mv ipinfo_2.0.1_linux_amd64 /…   9.36MB    buildkit.dockerfile.v0
<missing>      4 minutes ago   RUN /bin/sh -c tar zxf ipinfo_2.0.1_linux_am…   9.36MB    buildkit.dockerfile.v0
<missing>      4 minutes ago   RUN /bin/sh -c wget https://github.com/ipinf…   4.85MB    buildkit.dockerfile.v0
<missing>      4 minutes ago   RUN /bin/sh -c apt-get install -y wget # bui…   7.58MB    buildkit.dockerfile.v0
<missing>      4 minutes ago   RUN /bin/sh -c apt-get update # buildkit        33MB      buildkit.dockerfile.v0
<missing>      4 days ago      /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>      4 days ago      /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B
<missing>      4 days ago      /bin/sh -c [ -z "$(apt-get indextargets)" ]     0B
<missing>      4 days ago      /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   811B
<missing>      4 days ago      /bin/sh -c #(nop) ADD file:d6b6ba642344138dc…   74.1MB
```

每一行的**RUN**命令都会产生一层**image layer**, 导致镜像的臃肿。

所以我们就需要 **改进版Dockerfile**

```
FROM ubuntu:20.04
RUN apt-get update && \
    apt-get install -y wget && \
    wget https://github.com/ipinfo/cli/releases/download/ipinfo-2.0.1/ipinfo_2.0.1_linux_amd64.tar.gz && \
    tar zxf ipinfo_2.0.1_linux_amd64.tar.gz && \
    mv ipinfo_2.0.1_linux_amd64 /usr/bin/ipinfo && \
    rm -rf ipinfo_2.0.1_linux_amd64.tar.gz
```

再来看看镜像分层大小

```
$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
ipinfo-new   latest    fe551bc26b92   5 seconds ago    124MB
ipinfo       latest    97bb429363fb   16 minutes ago   138MB
ubuntu       21.04     478aa0080b60   4 days ago       74.1MB
$ docker image history fe5
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
fe551bc26b92   16 seconds ago   RUN /bin/sh -c apt-get update &&     apt-get…   49.9MB    buildkit.dockerfile.v0
<missing>      4 days ago       /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>      4 days ago       /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B
<missing>      4 days ago       /bin/sh -c [ -z "$(apt-get indextargets)" ]     0B
<missing>      4 days ago       /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   811B
<missing>      4 days ago       /bin/sh -c #(nop) ADD file:d6b6ba642344138dc…   74.1MB
$
```

很明显层数和大小都显著减少

> **`docker history`**	 --显示镜像的历史


## 文件复制和目录操作 (ADD,COPY,WORKDIR) {#document-operate}

往镜像里复制文件有两种方式，**`COPY`** 和 **`ADD`** , 我们来看一下两者的不同。

### 复制普通文件 {#copy-normal-doc}

**`COPY`** 和 **`ADD`** 都可以把local的一个文件复制到镜像里，如果目标目录不存在，则会自动创建

```
FROM python:3.9.5-alpine3.13
COPY hello.py /app/hello.py
```

比如把本地的 hello.py 复制到 /app 目录下。 **/app这个folder不存在，则会自动创建**

### 复制压缩文件 {#copy-compress-doc}

**`ADD`** 比 **`COPY`**高级一点的地方就是，如果复制的是一个**`gzip`**等压缩文件时，**`ADD`**会帮助我们自动去解压缩文件。

```
FROM python:3.9.5-alpine3.13
ADD hello.tar.gz /app/
```

### 如何选择{#how-choice}

因此在 **`COPY`** 和 **`ADD`** 指令中选择的时候，可以遵循这样的原则，所有的文件复制均使用 **`COPY`** 指令，仅在需要自动解压缩的场合使用 **`ADD`**。


## 构建参数和环境变量 (ARG vs ENV) {#build-arg-env}

**`ARG`** 和 **`ENV`** 是经常容易被混淆的两个 **`Dockerfile`** 的语法，都可以用来设置一个 **“变量”**。 **但实际上两者有很多的不同**。

以当前**dockerfile**例子：

```
FROM ubuntu:20.04
RUN apt-get update && \
    apt-get install -y wget && \
    wget https://github.com/ipinfo/cli/releases/download/ipinfo-2.0.1/ipinfo_2.0.1_linux_amd64.tar.gz && \
    tar zxf ipinfo_2.0.1_linux_amd64.tar.gz && \
    mv ipinfo_2.0.1_linux_amd64 /usr/bin/ipinfo && \
    rm -rf ipinfo_2.0.1_linux_amd64.tar.gz
```

### ENV {#build-env}

```
FROM ubuntu:20.04
ENV VERSION=2.0.1
RUN apt-get update && \
    apt-get install -y wget && \
    wget https://github.com/ipinfo/cli/releases/download/ipinfo-${VERSION}/ipinfo_${VERSION}_linux_amd64.tar.gz && \
    tar zxf ipinfo_${VERSION}_linux_amd64.tar.gz && \
    mv ipinfo_${VERSION}_linux_amd64 /usr/bin/ipinfo && \
    rm -rf ipinfo_${VERSION}_linux_amd64.tar.gz
```

### ARG {#build-ARG}

```
FROM ubuntu:20.04
ARG VERSION=2.0.1
RUN apt-get update && \
    apt-get install -y wget && \
    wget https://github.com/ipinfo/cli/releases/download/ipinfo-${VERSION}/ipinfo_${VERSION}_linux_amd64.tar.gz && \
    tar zxf ipinfo_${VERSION}_linux_amd64.tar.gz && \
    mv ipinfo_${VERSION}_linux_amd64 /usr/bin/ipinfo && \
    rm -rf ipinfo_${VERSION}_linux_amd64.tar.gz
```

### 两者之间的区别 {#env-vs-arg}


{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-31/16802410071257.jpg" caption="ENV和ARG之间的区别" >}}

**`ARG`** 可以在镜像**build**的时候动态修改**value**, 通过 **`--build-arg`**

就像这样 ⬇️
```
$ docker image build -f .\Dockerfile-arg -t ipinfo-arg-2.0.0 --build-arg VERSION=2.0.0 .
$ docker image ls
REPOSITORY         TAG       IMAGE ID       CREATED          SIZE
ipinfo-arg-2.0.0   latest    0d9c964947e2   6 seconds ago    124MB
$ docker container run -it ipinfo-arg-2.0.0
root@b64285579756:/#
root@b64285579756:/# ipinfo version
2.0.0
```

**`ENV` 设置的变量可以在Image中保持，并在容器中的环境变量里**

## 容器启动命令 CMD {#container-command}

**CMD**可以用来设置容器启动时默认会执行的命令。

- 容器启动时默认执行的命令
- 如果 **`docker container run`** 启动容器时指定了其它命令，则**CMD**命令会被忽略
- 如果定义了多个CMD，只有最后一个会被执行。

还是以这个**dockerfile**为例子：
```
FROM ubuntu:20.04
ENV VERSION=2.0.1
RUN apt-get update && \
    apt-get install -y wget && \
    wget https://github.com/ipinfo/cli/releases/download/ipinfo-${VERSION}/ipinfo_${VERSION}_linux_amd64.tar.gz && \
    tar zxf ipinfo_${VERSION}_linux_amd64.tar.gz && \
    mv ipinfo_${VERSION}_linux_amd64 /usr/bin/ipinfo && \
    rm -rf ipinfo_${VERSION}_linux_amd64.tar.gz
```

我们 **build** 一个镜像 ipinfo， 然后启动容器进入交互模式 

```
$ docker image build -t ipinfo .
$ docker container run -it ipinfo
root@8cea7e5e8da8:/#
root@8cea7e5e8da8:/# pwd
/
root@8cea7e5e8da8:/#
```

默认进入到**shell**是因为在**ubuntu**的基础镜像里有定义**CMD**

docker image history 查看镜像历史信息
```
$docker image history ipinfo
IMAGE          CREATED        CREATED BY                                      SIZE      COMMENT
db75bff5e3ad   24 hours ago   RUN /bin/sh -c apt-get update &&     apt-get…   50MB      buildkit.dockerfile.v0
<missing>      24 hours ago   ENV VERSION=2.0.1                               0B        buildkit.dockerfile.v0
<missing>      7 days ago     /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>      7 days ago     /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B
<missing>      7 days ago     /bin/sh -c [ -z "$(apt-get indextargets)" ]     0B
<missing>      7 days ago     /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   811B
<missing>      7 days ago     /bin/sh -c #(nop) ADD file:d6b6ba642344138dc…   74.1MB
```

## 容器启动命令 ENTRYPOINT {#container-entrypoint}

**`ENTRYPOINT`** 也可以设置容器启动时要执行的命令，但是和**CMD**是有区别的。


- **CMD** 设置的命令，可以在 **`docker container run`** 时传入其它命令，覆盖掉 **CMD** 的命令，但是 **ENTRYPOINT** 所设置的命令是一定会被执行的。
- **ENTRYPOINT** 和 **CMD** 可以联合使用，**ENTRYPOINT** 设置执行的命令，**CMD** 传递参数

这是一个简单的**dockerfile**

```
FROM ubuntu:20.04
CMD ["echo", "hello docker"]
```

把上面的 **`Dockerfile build`** 成一个叫 **`demo-cmd`** 的镜象

```
$ docker image ls
REPOSITORY        TAG       IMAGE ID       CREATED      SIZE
demo-cmd          latest    5bb63bb9b365   8 days ago   74.1MB
```

```
FROM ubuntu:20.04
ENTRYPOINT ["echo", "hello docker"]
```

然后把这个 **`build`** 成一个叫 **`demo-entrypoint`** 的镜像


```
$ docker image ls
REPOSITORY        TAG       IMAGE ID       CREATED      SIZE
demo-entrypoint   latest    b1693a62d67a   8 days ago   74.1MB
```

**CMD**的镜像，如果执行创建容器，不指定运行时的命令，则会默认执行**CMD**所定义的命令，打印出**`hello docker`**

但是如果我们 **`docker container run`** 的时候指定命令，则该命令会覆盖掉**CMD**的命令，如：

```
$ docker container run -it --rm demo-cmd echo "hello world"
hello world
```

但是 **ENTRYPOINT** 的容器里 **ENTRYPOINT** 所定义的命令则无法覆盖，一定会执行

如下：
```
$ docker container run -it --rm demo-entrypoint
hello docker
$ docker container run -it --rm demo-entrypoint echo "hello world"
hello docker echo hello world
$
```

**而且指定运行时的命令也会被执行**


## Shell 格式和 Exec 格式 {#shell-and-exec}


**CMD**和**ENTRYPOINT**同时支持**shell**格式和**Exec**格式。

### Shell格式 {#shell-format}

```shell
CMD echo "hello docker"
````

```shell
ENTRYPOINT echo "hello docker"
```

### Eexc格式 {#exec-format}

以可执行命令的方式

```
ENTRYPOINT ["echo", "hello docker"]
```

```
CMD ["echo", "hello docker"]
```

**注意shell脚本的问题**

```
FROM ubuntu:20.04
ENV NAME=docker
CMD echo "hello $NAME"
```

假如我们要把上面的**CMD**改成**Exec**格式，下面这样改是不行的, 大家可以试试。

```
FROM ubuntu:20.04
ENV NAME=docker
CMD ["echo", "hello $NAME"]
```

它会打印出 **` hello $NAME`** , 而不是 **`hello docker`** ,那么需要怎么写呢？ 

我们需要以**shell**脚本的方式去执行

```
FROM ubuntu:20.04
ENV NAME=docker
CMD ["sh", "-c", "echo hello $NAME"]
```

这样才可以


## 一起构建一个 Python Flask 镜像 {#build-py-flask}


这是是一个 **Python Flask** **Dockerfile**的例子
### Python 程序 {#py-f}

```
from flask import Flask

app = Flask(__name__)


@app.route('/')
def hello_world():
    return 'Hello, World!'
    
```

### Dockerfile {#dkf}

```
FROM python:3.9.5-slim

COPY app.py /src/app.py

RUN pip install flask

WORKDIR /src
ENV FLASK_APP=app.py

EXPOSE 5000

CMD ["flask", "run", "-h", "0.0.0.0"]
```

这样我们就可以通过 `docker image build .` 构建镜像，虽然这个镜像没有问题，但其实还能对它进行优化


## Dockerfile 技巧——合理使用缓存 {#dockerfile-cache}


以这个**Dockerfile**为例子

```
FROM python:3.9.5-slim

COPY app.py /src/app.py

RUN pip install flask

WORKDIR /src
ENV FLASK_APP=app.py

EXPOSE 5000

CMD ["flask", "run", "-h", "0.0.0.0"]
```

> 我们可以通过**`history`**命令来查看镜像历史信息

当我们需要修改**Dockerfile**中某一层的内容的时候，之后的指令都不会使用**cache**，那么我们在构建镜像的时候就会需要重新构建，速度会相当缓慢


主要是因为当不使用**cache**的时候，**build**会相当耗时，因为后面的构建需要重新再来（下载安装），所以我们应该把容易发生改变的内容放在后面，不容易改变的放前面，可以大大提高**build**速度

> 尽量使用 **cache** 来提高 **build** 的速度，经常变化导致不能使用**cache**的内容往后放，以提高构建性能

这是修改后的例子:

```
FROM python:3.9.5-slim

RUN pip install flask

WORKDIR /src

ENV FLASK_APP=app.py

COPY app.py /src/app.py

EXPOSE 5000

CMD ["flask", "run", "-h", "0.0.0.0"]
```

## Dockerfile 技巧——合理使用 .dockerignore {#dockerignore}

### 什么是Docker build context {#build-context}

Docker是**client-server**架构，理论上**Client**和**Server**可以不在一台机器上。

在构建**docker**镜像的时候，需要把所需要的文件由**CLI（client）** 发给**Server**，这些文件实际上就是 **build context** 

举例：
```
$ more Dockerfile
FROM python:3.9.5-slim

RUN pip install flask

WORKDIR /src
ENV FLASK_APP=app.py

COPY app.py /src/app.py

EXPOSE 5000

CMD ["flask", "run", "-h", "0.0.0.0"]


$ more app.py

from flask import Flask

app = Flask(__name__)


@app.route('/')
def hello_world():
    return 'Hello, world!'
    
```

构建的时候，第一行输出就是发送 **`build context`**  11.13MB 

（这里是Linux环境下的log）
```
$ docker image build -t demo .
Sending build context to Docker daemon  11.13MB
Step 1/7 : FROM python:3.9.5-slim
 ---> 609da079b03a
Step 2/7 : RUN pip install flask
 ---> Using cache
 ---> 955ce495635e
Step 3/7 : WORKDIR /src
 ---> Using cache
 ---> 1c2f968e9f9b
Step 4/7 : ENV FLASK_APP=app.py
 ---> Using cache
 ---> dceb15b338cf
Step 5/7 : COPY app.py /src/app.py
 ---> Using cache
 ---> 0d4dfef28b5f
Step 6/7 : EXPOSE 5000
 ---> Using cache
 ---> 203e9865f0d9
Step 7/7 : CMD ["flask", "run", "-h", "0.0.0.0"]
 ---> Using cache
 ---> 35b5efae1293
Successfully built 35b5efae1293
Successfully tagged demo:latest

```

> `.` 这个参数就是代表了 **`build context`** 所指向的目录

### .dockerignore 文件 {#dockerignore-file}

```
.vscode/
env/
```

有了 **`.dockerignore`** 文件后，我们再 **build**, **`build context`** 就小了很多，4.096kB

如下：

```
$ docker image build -t demo .
Sending build context to Docker daemon  4.096kB
Step 1/7 : FROM python:3.9.5-slim
---> 609da079b03a
Step 2/7 : RUN pip install flask
---> Using cache
---> 955ce495635e
Step 3/7 : WORKDIR /src
---> Using cache
---> 1c2f968e9f9b
Step 4/7 : ENV FLASK_APP=app.py
---> Using cache
---> dceb15b338cf
Step 5/7 : COPY . /src/
---> a9a8f888fef3
Step 6/7 : EXPOSE 5000
---> Running in c71f34d32009
Removing intermediate container c71f34d32009
---> fed6995d5a83
Step 7/7 : CMD ["flask", "run", "-h", "0.0.0.0"]
---> Running in 7ea669f59d5e
Removing intermediate container 7ea669f59d5e
---> 079bae887a47
Successfully built 079bae887a47
Successfully tagged demo:latest

```

我们可以通过 **`.dockerignore`** 忽略不需要的文件目录，和 **`.gitignore`** 差不多


## Docker 小技巧 -- 快速清空 （镜像或容器）{#prune}


`docekr system prune -f . `  命令可以用于清理 **Docker** 系统中未使用的资源，包括未被任何容器使用的卷、网络、镜像和停止运行的容器等。它可以帮助您释放磁盘空间和提高系统性能。

> 请注意，使用 -f 或 --force 标志 ，此命令将不可逆地删除所有未使用的资源，因此请谨慎使用它。

- `docker container prune`：清理未使用的容器
- `docker image prune`：清理未使用的镜像
- `docker volume prune`：清理未使用的卷
- `docker network prune`：清理未使用的网络

## Dockerfile 技巧——镜像的多阶段构建 {#dockerfile-more-build}

这一节来聊聊多阶段构建，以及为什么要使用它。

### C语言例子 {#clang-demo}

假如有一个C的程序，我们想用**Docker**去做编译，然后执行可执行文件。

```c
#include <stdio.h>

void main(int argc, char *argv[])
{
    printf("hello %s\n", argv[argc - 1]);
}

```

本地测试（如果你本地有C环境）

```
$ gcc --static -o hello hello.c
$ ls
hello  hello.c
$ ./hello docker
hello docker
$ ./hello world
hello world
$ ./hello friends
hello friends
$
```

构建一个**Docker**镜像，因为要有**C**的环境，所以我们选择**gcc**这个**image**

```
FROM gcc:9.4

COPY hello.c /src/hello.c

WORKDIR /src

RUN gcc --static -o hello hello.c

ENTRYPOINT [ "/src/hello" ]

CMD []
```

进行**build**和测试

```
$ docker build -t hello .
Sending build context to Docker daemon   5.12kB
Step 1/6 : FROM gcc:9.4
---> be1d0d9ce039
Step 2/6 : COPY hello.c /src/hello.c
---> Using cache
---> 70a624e3749b
Step 3/6 : WORKDIR /src
---> Using cache
---> 24e248c6b27c
Step 4/6 : RUN gcc --static -o hello hello.c
---> Using cache
---> db8ae7b42aff
Step 5/6 : ENTRYPOINT [ "/src/hello" ]
---> Using cache
---> 7f307354ee45
Step 6/6 : CMD []
---> Using cache
---> 7cfa0cbe4e2a
Successfully built 7cfa0cbe4e2a
Successfully tagged hello:latest
$ docker image ls
REPOSITORY     TAG          IMAGE ID       CREATED       SIZE
hello          latest       7cfa0cbe4e2a   2 hours ago   1.14GB
gcc            9.4          be1d0d9ce039   9 days ago    1.14GB
$ docker run --rm -it hello docker
hello docker
$ docker run --rm -it hello world
hello world
$ docker run --rm -it hello friends
hello friends
$
```

可以看到镜像非常的大，1.14GB

实际上当我们把**hello.c**编译完以后，并不需要这样一个大的**GCC**环境，一个小的**alpine**镜像就可以了。

**这时候我们就可以使用多阶段构建了。**

```
FROM gcc:9.4 AS builder

COPY hello.c /src/hello.c

WORKDIR /src

RUN gcc --static -o hello hello.c



FROM alpine:3.13.5

COPY --from=builder /src/hello /src/hello

ENTRYPOINT [ "/src/hello" ]

CMD []
```

然后测试一下：

```
$ docker build -t hello-apline -f Dockerfile-new .
Sending build context to Docker daemon   5.12kB
Step 1/8 : FROM gcc:9.4 AS builder
---> be1d0d9ce039
Step 2/8 : COPY hello.c /src/hello.c
---> Using cache
---> 70a624e3749b
Step 3/8 : WORKDIR /src
---> Using cache
---> 24e248c6b27c
Step 4/8 : RUN gcc --static -o hello hello.c
---> Using cache
---> db8ae7b42aff
Step 5/8 : FROM alpine:3.13.5
---> 6dbb9cc54074
Step 6/8 : COPY --from=builder /src/hello /src/hello
---> Using cache
---> 18c2bce629fb
Step 7/8 : ENTRYPOINT [ "/src/hello" ]
---> Using cache
---> 8dfb9d9d6010
Step 8/8 : CMD []
---> Using cache
---> 446baf852214
Successfully built 446baf852214
Successfully tagged hello-apline:latest
$ docker image ls
REPOSITORY     TAG          IMAGE ID       CREATED       SIZE
hello-alpine   latest       446baf852214   2 hours ago   6.55MB
hello          latest       7cfa0cbe4e2a   2 hours ago   1.14GB
demo           latest       079bae887a47   2 hours ago   125MB
gcc            9.4          be1d0d9ce039   9 days ago    1.14GB
$ docker run --rm -it hello-alpine docker
hello docker
$ docker run --rm -it hello-alpine world
hello world
$ docker run --rm -it hello-alpine friends
hello friends
$
```

可以看到这个镜像非常小，只有6.55MB


其他的编译语言也是一样，例如Golang也是同理，进行多阶段构建可以减少镜像大小

## Dockerfile 技巧——尽量使用非root用户 {#dockerfile-not-root}

### Root的危险性 {#root-danger}

**docker**的**root**权限一直是其遭受诟病的地方，**docker**的**root**权限有那么危险么？我们举个例子。

假如我们有一个用户，叫demo，它本身不具有sudo的权限，所以就有很多文件无法进行读写操作，比如/root目录它是无法查看的。

```
[demo@docker-host ~]$ sudo ls /root
[sudo] password for demo:
demo is not in the sudoers file.  This incident will be reported.
[demo@docker-host ~]$
```

**但是这个用户有执行docker的权限，也就是它在docker这个group里。**


```
[demo@docker-host ~]$ groups
demo docker
[demo@docker-host ~]$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
busybox      latest    a9d583973f65   2 days ago   1.23MB
[demo@docker-host ~]$
```

这时，我们就可以通过**Docker**做很多越权的事情了，**比如，我们可以把这个无法查看的/root目录映射到docker container里，你就可以自由进行查看了。**

```
[demo@docker-host vagrant]$ docker run -it -v /root/:/root/tmp busybox sh
/ # cd /root/tmp
~/tmp # ls
anaconda-ks.cfg  original-ks.cfg
~/tmp # ls -l
total 16
-rw-------    1 root     root          5570 Apr 30  2020 anaconda-ks.cfg
-rw-------    1 root     root          5300 Apr 30  2020 original-ks.cfg
~/tmp #
```

更甚至我们可以给我们自己加**sudo**权限。

我们现在没有**sudo**权限

```
[demo@docker-host ~]$ sudo vim /etc/sudoers
[sudo] password for demo:
demo is not in the sudoers file.  This incident will be reported.
[demo@docker-host ~]$

```
但是我可以给自己添加。

```
[demo@docker-host ~]$ docker run -it -v /etc/sudoers:/root/sudoers busybox sh
/ # echo "demo    ALL=(ALL)       ALL" >> /root/sudoers
/ # more /root/sudoers | grep demo
demo    ALL=(ALL)       ALL

```

然后退出container，bingo，**我们有sudo权限了**。


```
[demo@docker-host ~]$ sudo more /etc/sudoers | grep demo
demo    ALL=(ALL)       ALL
[demo@docker-host ~]$

```

### 如何使用非root用户 {#use-not-root}

我们准备两个**Dockerfile**，第一个**Dockerfile**如下：

```
FROM python:3.9.5-slim

RUN pip install flask

COPY app.py /src/app.py

WORKDIR /src
ENV FLASK_APP=app.py

EXPOSE 5000

CMD ["flask", "run", "-h", "0.0.0.0"]
```

假设构建的镜像名字为 `flask-demo`

第二个**Dockerfile**，使用**非root**用户来构建这个镜像，名字叫 `flask-no-root ` **Dockerfile**如下：

- 通过**groupadd**和**useradd**创建一个**flask**的组和用户
- 通过**USER**指定后面的命令要以**flask**这个用户的身份运行

```
FROM python:3.9.5-slim

RUN pip install flask && \
    groupadd -r flask && useradd -r -g flask flask && \
    mkdir /src && \
    chown -R flask:flask /src

USER flask

COPY app.py /src/app.py

WORKDIR /src
ENV FLASK_APP=app.py

EXPOSE 5000

CMD ["flask", "run", "-h", "0.0.0.0"]
```

```
$ docker image ls
REPOSITORY      TAG          IMAGE ID       CREATED          SIZE
flask-no-root   latest       80996843356e   41 minutes ago   126MB
flask-demo      latest       2696c68b51ce   49 minutes ago   125MB
python          3.9.5-slim   609da079b03a   2 weeks ago      115MB
```

分别使用这两个镜像创建两个容器


```
$ docker run -d --name flask-root flask-demo
b31588bae216951e7981ce14290d74d377eef477f71e1506b17ee505d7994774
$ docker run -d --name flask-no-root flask-no-root
83aaa4a116608ec98afff2a142392119b7efe53617db213e8c7276ab0ae0aaa0
$ docker container ps
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS          PORTS      NAMES
83aaa4a11660   flask-no-root   "flask run -h 0.0.0.0"   4 seconds ago    Up 3 seconds    5000/tcp   flask-no-root
b31588bae216   flask-demo      "flask run -h 0.0.0.0"   16 seconds ago   Up 15 seconds   5000/tcp   flask-root
```

这样就可以做到限制使用root给系统带来风险

> `docker container top` --查看进程

## dockerfile 资料 {#dockerfile-data}


docker官网 **dockerfile** 文档
[https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)


docker官方镜像 **docker-library**
[https://github.com/docker-library](https://github.com/docker-library)

---
<center > - END - </center>

