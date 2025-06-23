# FRP 服务端配置

[TOC]

## 一、介绍

[frp](https://github.com/fatedier/frp) 是一个专注于内网穿透的高性能的反向代理应用，支持 TCP、UDP、HTTP、HTTPS 等多种协议，且支持 P2P 通信。可以将内网服务以安全、便捷的方式通过具有公网 IP 节点的中转暴露到公网。

## 二、安装

前往 [release](https://github.com/fatedier/frp/releases) 获取最新版本

```shell
wget https://github.com/fatedier/frp/releases/download/v0.62.1/frp_0.62.1_linux_amd64.tar.gz
tar -xvf frp_0.62.1_linux_amd64.tar.gz
chmod u+x frp_0.62.1_linux_amd64/frps

sudo mkdir -p /opt/frp/
sudo mkdir -p /var/log/frp
sudo mv frp_0.62.1_linux_amd64/frps /opt/frp
sudo mv frp_0.62.1_linux_amd64/frps.toml /opt/frp
```

## 三、配置

编辑配置文件 `/opt/frp/frps.toml`

```toml
# 必选项
# 监听地址和端口
bindAddr = "0.0.0.0"
bindPort = {port}

# 选配项
# web 后台信息管理页面
webServer.addr = "0.0.0.0"
webServer.port = 56000
webServer.user = "{user}"
webServer.password = "{password}"

# 必填项
# 客户端认证方式和开放端口
log.to = "console"
auth.method = "token"
auth.token = "{secret}"
allowPorts = [{ start = 54000, end = 56000 }]
```

## 四、启动

配置服务守护进程`/etc/systemd/system/frps.service`

```ini
[Unit]
Description=FRP Server Service
After=network.target

[Service]
Type=simple
Restart=always
RestartSec=10
WorkingDirectory=/opt/frp/
ExecStart=/opt/frp/frps -c /opt/frp/frps.toml
StandardOutput=append:/var/log/frp/frps.log
StandardError=append:/var/log/frp/frps.error
LimitNOFILE=1024
MemoryLimit=200M
TimeoutStartSec=30
TimeoutStopSec=30

[Install]
WantedBy=multi-user.target
```

设置开机自启

```shell
sudo systemctl enable frps.service
```

启动服务

```shell
sudo systemctl start frps.service
```

查看服务状态

```shell
sudo systemctl status frps.service
```

