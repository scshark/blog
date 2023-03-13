---
title: "Quick Reference"
date: 2023-03-10T17:05:41+08:00
lastmod: 2023-03-10T17:05:41+08:00
draft: false
authors: ["scshark"]
description: "为开发人员分享快速参考备忘清单(速查表)"
tags: ["Javascript","Docker","Npm","React","TypeScript"]
categories: ["Javascript"]
series: []

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "https://blog.cdn.scshark.com/scshark/2023-03-10/16784396127631.jpg"
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2023-03-10/16784396456551.jpg"

toc:
  enable: true
math:
  enable: false
lightgallery: true
license: ""

---

<!--more-->

## 快捷部署方法{#quick_deploy}

### 方法一、只需要克隆 gh-pages 分支代码到你的静态服务就可以了

```
git clone https://github.com/jaywcjlove/reference.git -b gh-pages
```

你可以使用 **gitee** 镜像仓库获取最新的代码

```
git clone https://gitee.com/jaywcjlove/reference.git -b gh-pages
```
---
### 方法二、使用 docker 快捷部署 web 版

轻松通过 docker 部署 Quick Reference 网站。

```
docker pull wcjiang/reference

docker run --name reference --rm -d -p 9667:3000 wcjiang/reference:latest
# Or
docker run --name reference -itd -p 9667:3000 wcjiang/reference:latest
```

在浏览器中访问以下 URL

> http://localhost:9667/
---
### 方法三、克隆仓库自己编译，添加导航菜单

```
git clone https://github.com/jaywcjlove/reference.git 
npm install    # 安装依赖
npm run build  # 编译输出静态页面
npm run start  # 开发模式，监听实时编译输出静态页面
# 添加环境变量文件，添加环境变量
touch .env
```

文件被输出到 **dist** 目录，将 **dist** 目录静态页面部署到静态服务就可以了

{{< image src="https://blog.cdn.scshark.com/scshark/2023-03-10/16784403202088.jpg" >}}


提供自定义菜单，在项目的根目录建立 **.env** 文件，添加下面内容

```
REF_URL=http://ref.xxx.cn/
REF_LABEL=网站首页

```

页脚添加 (支持 HTML 字符串)

```
REF_FOOTER=备案号：沪ICP备20220000000号-1
```

修改版权信息 (支持 HTML 字符串)

```
LICENSE=Copyright (c) <b>2022</b> 小弟调调™
```


## 利用 Github Actions 定时任务来完成自动更新 {#accomplish_auto_update}

在仓库添加 **`.github/workflows/update-ref.yml`** 文件 **Github Actions** 配置

```yaml
name: 每8个小时更新一次reference
on:
  schedule:
    - cron: '21 */8 * * *' # 定时任务
  workflow_dispatch:       # 手动运行

env: # 设置环境变量
  TZ: Asia/Shanghai # 时区（设置时区可使页面中的`最近更新时间`使用时区时间）

jobs:
  build: # 自定义名称
    runs-on: ubuntu-latest
    steps:
      - name: 🚜 拉取最新代码
        uses: actions/checkout@v3
        with:
          ref: 'main'
          repository: 'jaywcjlove/reference'

      - name: ♻️ 编译静态文件
        run: |
          echo -e 'REF_URL=https://refs.xxx.net/\nREF_LABEL=网站首页' > .env
          npm install
          npm run build

      - name: 🚁 部署到服务器
        uses: wlixcc/SFTP-Deploy-Action@v1.0
        with:
          username: 'root'   #ssh user name
          port: '22' # 远程服务器ssh端口，默认22
          server: 'prod.refs.xxx.net' # 远程服务器IP
          ssh_private_key: ${{ secrets.SSH_PRIVATE_KEY }} # 认证服务器秘钥对的私钥
          local_path: './dist/*'  # 对应我们项目打包后的静态文件路径
          remote_path: '/data/www/refs.xxx.net' # 服务器上的路径
          delete_remote_files: true
          
```

## 利用 Github Actions 分支main-push更新到Cos {#accomplish_auto_update}

```yaml
name: 上传reference
on:
  push:
    branches:
      - main
  workflow_dispatch:

env: # 设置环境变量
  TZ: Asia/Shanghai # 时区（设置时区可使页面中的`最近更新时间`使用时区时间）

jobs:
  build: # 自定义名称
    runs-on: ubuntu-latest
    steps:
      - name: 🚜 拉取最新代码
        uses: actions/checkout@v3
        with:
          ref: 'main'
          repository: 'scshark/quick_reference'

      - name: ♻️ 编译静态文件
        run: |
          echo -e 'REF_URL=https://scshark.com/\nREF_LABEL=网站首页\nREF_FOOTER=备案号：粤ICP备19136083号\nLICENSE=Copyright (c) <b>2023</b> scshark' > .env
          npm install
          npm run build

      - name: Upload COS
        uses: zkqiang/tencent-cos-action@v0.1.0
        with:
          args: delete -r -f / && upload -r ./dist/ /
          secret_id: ${{ secrets.SECRET_ID }}
          secret_key: ${{ secrets.SECRET_KEY }}
          bucket: ${{ secrets.BUCKET }}
          region: ap-guangzhou
```











---
<center > - END - </center>
