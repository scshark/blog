---
title: "Ubuntu安装Yarn"
date: 2023-03-10T13:25:50+08:00
lastmod: 2023-03-10T13:25:50+08:00
draft: false
authors: ["scshark"]
description: "Yarn是facebook开源的JS(JavaScript)包管理器，它使用node.js来跟踪库和依赖项。"
tags: ["Ubuntu","Yarn","Node","JavaScript"]
categories: ["Ubuntu"]
series: []

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "https://blog.cdn.scshark.com/scshark/2023-03-10/16784307481196.jpg"
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2023-03-10/16784307481196.jpg"

toc:
  enable: true
math:
  enable: false
lightgallery: false
license: ""

---

Yarn是facebook开源的JS(JavaScript)包管理器，它使用node.js来跟踪库和依赖项。

<!--more-->


## ubuntu安装yarn最新版 {#install_yarn}

### 步骤一：确保node.js版本高于16.1 {#install_yarn_step_one}

```
node -v
```

如果低于16.1，先更新node.js

### 步骤二：启动核心包 {#install_yarn_step_two}

```
corepack enable
```

如果系统不存在Corepack，则需要先安装


```
npm install -g corepack

```

### 步骤三：安装最新版Yarn {#install_yarn_step_three}

```
corepack prepare yarn@stable --activate

```

### 步骤四：检查是否安装成功和版本 {#install_yarn_step_four}

```
yarn --version
```

### 步骤五：将yarn二进制文件更新至最新版本 {#install_yarn_step_five}

```
yarn set version stable

```

## ubuntu安装yarn 1.x经典版本 {#install_yarn_classic}

**yarn 2.0之前的版本，我们称之为经典版本，它仍然处于维护状态，当然，其支持即将结束。**


### 方法一：通过储存库安装Yarn Classic {#classic_1}

步骤一：增加GPG密钥

```
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | tee /etc/apt/trusted.gpg.d/yarn.gpg

```

步骤二：增加Yarn储存库

```
echo "deb [signed-by=/etc/apt/trusted.gpg.d/yarn.gpg] https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
```

步骤三：更新储存库

```
apt update
```

步骤四：安装yarn


```
apt install yarn -y
```

步骤五：检查安装的版本

```
yarn ––version
```

### 方法二：通过NPM安装yarn classic {#classic_2}

步骤一：确认是否安装了npm

```
npm ––version
```

如果没有安装，运行以下命令安装。

```
apt install npm

```

步骤二：安装yarn

```
npm install -g yarn
```

Yarn在很多地方和npm很像。Yarn的`yarn.lock`文件，可以将包限制为特定版本。

## 将yarn classic版本升级至最新版本 {#upgrade_classic}

步骤一：先将yarn经典版更新至最新

```
npm install -g yarn
```

步骤二：切换yarn版本为最新版本

```
yarn set version berry
```


## yarn的一些基本用法{#yarn_usage}

### 创建新项目 {#create_new}

先cd到要创建项目的目录，然后初始化

```
yarn init
```

此时会创建两个文件， `package.json`和`yarn.lock`。

`package.json`用于项目配置，`yarn.lock`用于储存依赖关系相关信息。


### 向项目添加依赖项 {#project_add}

```
yarn add [package-name]
```
示例
```
yarn add npm
```

**安装特定版本**

```
yarn add [package-name]@version
```

**安装带有标记的**

```
yarn add [package-name]@tag
```


### 升级依赖项{#upgrade_depend}

```
yarn upgrade [package-name]
```

**升级特定版本**

```
yarn upgrade [package-name]@version
```

**升级带标记的**

```
yarn upgrade [package-name]@tag

```
### 删除依赖项 {#delete_depend}

```
yarn remove [package-name]

```

### 安装其他依赖项{#install_other_depend}

先在`package.json`里编辑，然后运行以下命令安装


```
yarn install
```

---
<center > - END - </center>