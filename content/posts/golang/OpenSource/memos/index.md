---
title: "Memos - 备忘录"
date: 2023-03-10T16:17:24+08:00
lastmod: 2023-03-10T16:17:24+08:00
draft: false
authors: ["scshark"]
description: "一个开源的、自托管的备忘录中心，具有知识管理和社交网络功能。"
tags: ["React","Go","Docker","Markdown","Memo"]
categories: ["Golang"]
series: []

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "https://blog.cdn.scshark.com/scshark/2023-03-10/16784367797543.jpg"
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2023-03-10/16784367729342.jpg"

toc:
  enable: true
math:
  enable: false
lightgallery: true
license: ""

---


<!--more-->

**<center> <font size=5> 一个轻量级的、自托管的备忘录中心，开源且永久免费。</font></center>**


**<center><font color=gray size=3 >  备忘录为创新者在灵感迸发时所需的隐私安全和可靠性提供保障。</font></center>**


## 亮点 {#key-point}

- 开源且永久免费

- 使用 Docker 可在几秒钟内自主托管

- 支持 Markdown 格式

- 可定制和共享的

- RESTful API 供自助服务使用

## Docker run {#docker-run}


使用docker run部署备忘录，您只需要运行一个命令：


```
docker run -d --name memos -p 5230:5230 -v ~/.memos/:/var/opt/memos neosmemo/memos:latest
```

这将在后台启动memos，并在端口5230上公开。**数据将存储在~/.memos/中。您可以根据需要更改端口和数据目录的路径。但是，只更改第一个端口，例如8081:5230以使用端口8081。第二个端口是容器内备忘录正在侦听的端口。对于目录也是如此。第一个路径是主机系统上的路径，第二个路径是容器内部的路径。**

## Docker compose{#docker-compose}

要使用Docker Compose部署备忘录，您需要创建一个名为 **`docker-compose.yml`** 的文件，并包含以下内容：

```yaml
version: "3.0"
services:
  memos:
    image: neosmemo/memos:latest
    container_name: memos
    volumes:
      - ~/.memos/:/var/opt/memos
    ports:
      - 5230:5230
      
```

现在你可以运行 **`docker-compose up -d `** 来启动 memos。


**您可以根据自己的喜好编辑端口和数据目录的路径**。但是，请仅更改第一个端口，例如将 **`8081:5230`** 更改为使用端口 **8081** 。第二个端口是容器内memos正在侦听的端口。对于目录也是如此。第一个路径是主机系统上的路径，而第二个路径则是容器内部的路径。

您可以使用 **`docker-compose up -d `** 开始**memos**。


## 使用nginx作为反向代理 {#use_nginx}

一旦您运行了memos，您可以使用nginx创建反向代理，将域名连接到您的实例。

要做到这一点，首先安装nginx。然后，创建一个名为 **`/etc/nginx/sites-available/your-domain-name.com`** 的文件，并将以下内容添加进去：

```nginx
server {
    server_name your-domain-name.com;

    location / {
        proxy_pass http://localhost:5230;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

```

现在，您可以使用 **`sudo ln -s /etc/nginx/sites-available/your-domain-name.com /etc/nginx/sites-enabled/your-domain-name.com`** 启用该站点。然后，您可以使用  **`sudo systemctl restart nginx `** 重新启动nginx。

### SSL{#use_ssl}

应用 SSL 证书的最简单方法是使用 **Let's Encrypt**。您可以使用** `Certbot` **获取证书。要做到这一点，首先使用 **`sudo apt install certbot`** 安装 **`certbot`**。然后，您可以使用 **`sudo certbot --nginx -d`** 您的域名.com 来获取证书。确保域名已经指向了您的服务器。**Certbot** 将尝试创建并安装证书到您的 **nginx** 配置中。如果成功完成此操作，则会在配置文件中添加几行类似于以下内容的代码：

```nginx
listen 443 ssl; # managed by Certbot
ssl_certificate /etc/letsencrypt/live/your-domain-name.com/fullchain.pem; # managed by Certbot
ssl_certificate_key /etc/letsencrypt/live/your-domain-name.com/privkey.pem; # managed by Certbot
include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

```

如果出现这些行，则可以使用 **`sudo systemctl restart nginx`** 重新启动 **nginx**。 SSL现在应该可用。




---
<center > - END - </center>






