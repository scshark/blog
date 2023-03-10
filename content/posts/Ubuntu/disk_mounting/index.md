---
title: "Ubuntu新磁盘挂载"
date: 2023-03-10T13:25:11+08:00
lastmod: 2023-03-10T13:25:11+08:00
draft: false
authors: ["scshark"]
description: "Ubuntu下挂载一个新硬盘的基本步骤"
tags: ["Ubuntu","disk"]
categories: ["Ubuntu"]
series: []

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "https://blog.cdn.scshark.com/scshark/2023-03-10/16784274727225.jpg"
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2023-03-10/16784274727225.jpg"

toc:
  enable: true
math:
  enable: false
lightgallery: true
license: ""

---
磁盘挂载
<!--more-->

### 概述 {#overview}

Ubuntu下挂载一个新硬盘的基本步骤是：

- 给硬盘创建分区
- 给硬盘创建文件系统
- 挂载移动硬盘

需要用到的命令：

```

lsblk   #查看所有硬盘情况
df -lh  #查看硬盘占用情况，以及挂载位置
sudo fdisk -l #查看硬盘分区情况
sudo fdisk /dev/vdb #给硬盘vdb创建分区
sudo mkfs.ext4 /dev/vdb #格式化硬盘vdb，并写入文件系统
sudo mount /dev/vdb /scshark #将硬盘vdb挂载到/scshark
```

### 查看硬盘情况 {#check_disk}

```
lsblk
df -lh
```

若已挂载，则通过 `df -lh` 可以看到硬盘的挂载位置

若未挂载，则只能通过 `lsblk` 查看到硬盘。

### 创建分区 {#create_partition}

```
sudo fdisk /dev/vdb #给硬盘vdb创建分区
```

根据提示，依次输入：**n、p、1、回车、回车、w** 。

意思是新建一个主分区，大小是整个vdb硬盘，然后保存退出。

或者根据自己磁盘情况，自己主分区、扩展分区、逻辑分区

### 格式化硬盘，并写入文件系统 {#format}

```
sudo mkfs.ext4 /dev/vdb #格式化硬盘vdb，并写入文件系统
```

上述命令会格式化硬盘分区为`ext4`的文件系统。

### 挂载硬盘 {#mount}
```
sudo mkdir /scshark
sudo mount /dev/vdb /scshark #将硬盘vdb挂载到/scshark
```
上述命令将硬盘挂载到 **/scshark** 目录下，然后就可以使用了。目录可以自己选择，创建哪里。

### 设置开机自动挂载 {#set_enable}

为了使硬盘开机能够自动挂载，还需打开文件 `/etc/fstab`

#### 查看UUID {#blk}

```
blkid

blkid /dev/vdb #查看指定分区
```

#### 配置系统文件表 {#fstab}

打开/etc/fstab，根据对应的格式如下把UUID，配置到文件中；

```
UUID=9c72c786-d77b-4f15-9022-1ccf71a4dce8 /scshark ext4 errors=remount-ro 0 0 
```

配置完之后如下图所，记得保存：

```
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda2 during curtin installation
#/dev/disk/by-uuid/b986dc3b-6b82-44d5-acb8-6cbad5e357d5 / ext4 defaults 0 0
UUID=b986dc3b-6b82-44d5-acb8-6cbad5e357d5 / ext4 defaults 0 0
UUID=9c72c786-d77b-4f15-9022-1ccf71a4dce8 /scshark ext4 errors=remount-ro 0 0 
```

**添加信息时，注意之间的空白处使用 `table` 键，信息核对无误后保存退出即可。**


---
<center > - END - </center>