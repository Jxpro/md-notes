# FRP 客户端配置

[TOC]

## 一、介绍

[frp](https://github.com/fatedier/frp) 是一个专注于内网穿透的高性能的反向代理应用，支持 TCP、UDP、HTTP、HTTPS 等多种协议，且支持 P2P 通信。可以将内网服务以安全、便捷的方式通过具有公网 IP 节点的中转暴露到公网。

## 二、安装

前往 [release](https://github.com/fatedier/frp/releases) 获取最新版本

```shell
wget https://github.com/fatedier/frp/releases/download/v0.62.1/frp_0.62.1_linux_amd64.tar.gz
tar -xvf frp_0.62.1_linux_amd64.tar.gz
chmod u+x frp_0.62.1_linux_amd64/frpc

sudo mkdir -p /opt/frp/
sudo mkdir -p /var/log/frp
sudo mv frp_0.62.1_linux_amd64/frpc /opt/frp
sudo mv frp_0.62.1_linux_amd64/frpc.toml /opt/frp
```

## 三、配置

编辑配置文件 `/opt/frp/frpc.toml`

```toml
# 用户标识
user = "{user}"

# 服务器地址
serverAddr = "{domain/ip}"
serverPort = {port}

# 客户端认证方式
log.to = "console"
auth.method = "token"
auth.token = "{secret}"

# 代理类型
[[proxies]]
name = "ssh"
type = "tcp"
localIP = "127.0.0.1"
localPort = 22
remotePort = {port}

transport.bandwidthLimit = "1MB"
transport.bandwidthLimitMode = "client"
transport.useEncryption = true
transport.useCompression = true
```

## 四、启动

配置服务守护进程`/etc/systemd/system/frpc.service`

```ini
[Unit]
Description=FRP Client Service
After=network.target

[Service]
Type=simple
Restart=always
RestartSec=10
WorkingDirectory=/opt/frp/
ExecStart=/opt/frp/frpc -c /opt/frp/frpc.toml
StandardOutput=append:/var/log/frp/frpc.log
StandardError=append:/var/log/frp/frpc.error
LimitNOFILE=1024
MemoryLimit=200M
TimeoutStartSec=30
TimeoutStopSec=30

[Install]
WantedBy=multi-user.target
```

设置开机自启

```shell
sudo systemctl enable frpc.service
```

启动服务

```shell
sudo systemctl start frpc.service
```

查看服务状态

```shell
sudo systemctl status frpc.service
```