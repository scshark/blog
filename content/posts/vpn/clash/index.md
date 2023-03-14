---
title: "Clash"
date: 2023-03-07T17:56:48+08:00
lastmod: 2023-03-07T17:56:48+08:00
draft: false
authors: ["scshark"]
description: " A rule-based tunnel in Go. "
tags: ["tunnel","golang","clash","rule-based"]
categories: ["golang"]
series: []

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "https://blog.cdn.scshark.com/scshark/2023-03-07/16781830461703.jpg"
featuredImagePreview: "https://blog.cdn.scshark.com/scshark/2023-03-07/16781830461703.jpg"

toc:
  enable: true
math:
  enable: false
lightgallery: true
license: ""

---

<!--more-->

### 特征 {#Features}
- 具有身份验证支持的本地 HTTP/HTTPS/SOCKS 服务器
- Shadowsocks(R)、VMess、Trojan、Snell、SOCKS5、HTTP(S) 出站支持
- 内置假 IP DNS 服务器，旨在最大限度地减少 DNS 污染攻击的影响。支持 DoH/DoT 上游。
- 基于域、GEOIP、IP-CIDR 或进程名称的规则将数据包路由到不同的目的地
- 代理组允许用户实施强大的规则。支持自动回退、负载均衡或基于关闭延迟自动选择代理
- 远程提供商，允许用户远程获取代理列表而不是在配置中硬编码
- 透明代理：使用自动路由表/规则管理重定向 TCP 和 TProxy TCP/UDP
- 通过全面的 HTTP RESTful API 控制器进行热重载

### 开始 {#getting_started}

You can either grab the pre-built binaries of Clash from {{< link "https://github.com/Dreamacro/clash/releases" "https://github.com/Dreamacro/clash/releases">}} or build locally.

Clash requires Golang 1.19 or a higher version.

```
$ go install github.com/Dreamacro/clash@latest
```

The binary is built under $GOPATH/bin


```
$ clash -v
```


### 将 Clash 作为服务运行 {#running_clash_ser}


Clash 应该在后台运行，目前还没有办法用 Golang 优雅地实现守护进程。我们可以使用第三方工具守护 Clash。

#### systemd {#systemd}

Copy Clash binary to /usr/local/bin and configuration files to /etc/clash:


```
$ cp clash /usr/local/bin
$ cp config.yaml /etc/clash/
$ cp Country.mmdb /etc/clash/
```

Create the systemd configuration file at /etc/systemd/system/clash.service:


```
[Unit]
Description=Clash daemon, A rule-based proxy in Go.
After=network.target

[Service]
Type=simple
Restart=always
ExecStart=/usr/local/bin/clash -d /etc/clash

[Install]
WantedBy=multi-user.target
```

After that you're supposed to reload systemd:


```
$ systemctl daemon-reload
```

Launch clashd on system startup with:


```
$ systemctl enable clash
```

Launch clashd immediately with:

```
$ systemctl start clash
```

Check the health and logs of Clash with:

```
$ systemctl status clash
$ journalctl -xe
```

#### Docker {#Docker}

```
services:
  clash:
    # ghcr.io/dreamacro/clash
    # ghcr.io/dreamacro/clash-premium
    # dreamacro/clash
    # dreamacro/clash-premium
    image: dreamacro/clash
    container_name: clash
    volumes:
      - ./config.yaml:/root/.config/clash/config.yaml
      # - ./ui:/ui # dashboard volume
    ports:
      - "7890:7890"
      - "7891:7891"
      # - "8080:8080" # external controller (Restful API)
    # TUN
    # cap_add:
    #   - NET_ADMIN
    # devices:
    #   - /dev/net/tun
    restart: unless-stopped
    network_mode: "bridge" # or "host" on Linux
```
另存为`docker-compose.yaml`，在同一个目录下创建`config.yaml`，运行以下命令获取 Clash up：

```
$ docker-compose up -d
```

您可以使用以下命令查看日志：

```
$ docker-compose logs
```

停止冲突：


```
$ docker-compose stop
```

#### PM2.5 {#PM2.5}

```
$ wget -qO- https://getpm2.com/install.sh | bash
$ pm2 start clash
```


### 在你的golang程序里使用{#in_golang}

如果clash不适合自己的使用，可以在自己的Golang代码中使用Clash。 

已经有了基本的支持：

```
package main

import (
	"context"
	"fmt"
	"io"
	"net"

	"github.com/Dreamacro/clash/adapter/outbound"
	"github.com/Dreamacro/clash/constant"
	"github.com/Dreamacro/clash/listener/socks"
)

func main() {
	in := make(chan constant.ConnContext, 100)
	defer close(in)

	l, err := socks.New("127.0.0.1:10000", in)
	if err != nil {
		panic(err)
	}
	defer l.Close()

	println("listen at:", l.Address())

	direct := outbound.NewDirect()

	for c := range in {
		conn := c
		metadata := conn.Metadata()
		fmt.Printf("request incoming from %s to %s\n", metadata.SourceAddress(), metadata.RemoteAddress())
		go func () {
			remote, err := direct.DialContext(context.Background(), metadata)
			if err != nil {
				fmt.Printf("dial error: %s\n", err.Error())
				return
			}
			relay(remote, conn.Conn())
		}()
	}
}

func relay(l, r net.Conn) {
	go io.Copy(l, r)
	io.Copy(r, l)
}
```

### 通用 {#general}

#### Port {#port}

端口是计算机内部或交换路由器内部的一部分。 计算机按照INTERNET传输层TCP/IP协议的协议进行通信，不同的协议对应不同的端口。

> Redir Port only works at macOS and Linux.

```
# Port of HTTP(S) proxy server on the local end
port: 7890

# Port of SOCKS5 proxy server on the local end
socks-port: 7891

# Transparent proxy server port for Linux and macOS (Redirect TCP and TProxy UDP)
redir-port: 7892

# Transparent proxy server port for Linux (TProxy TCP and TProxy UDP)
tproxy-port: 7893

# HTTP(S) and SOCKS5 server on the same port
mixed-port: 7890
```
#### 允许局域网 {#allow_lan}

允许局域网内的其他设备通过 Clash 访问互联网。

```
allow-lan: false  # allow other devices access

bind-address: "*" # access by lan with a/some specific IP addresses
                  # "*": bind all IP addresses
                  # 192.168.122.11: bind a single IPv4 address
                  # "[aaaa::a8aa:ff:fe09:57d8]": bind a single IPv6 address

authentication:   # authentication of local SOCKS5/HTTP(S) server
  - "user1:pass1"
  - "user2:pass2"
```

#### Mode {#Mode}

```
mode: rule # rule / global / direct / script (default is rule)
```


#### 日志级别 {#log}

Clash Core 的日志级别。 Debug 将显示所有 DNS 查询和提供商更新日志，Debug 不会导致任何性能问题。

```
log-level: info # silent / error / warning / info / debug 
```

#### 外部控制 {#Exter}

外部控制器可以使用 RESTful API 控制来自外部的冲突


```
external-controller: 127.0.0.1:9090 

secret: "" # Secret for RESTful API (Optional)
```


#### 界面 {#ui}

你可以把静态的web资源（比如clash-dashboard）放到一个目录下，clash会在${API}/ui下服务

```
external-ui: folder #input is a relative path to the configuration directory or an absolute path
```



---
<center > - END - </center>