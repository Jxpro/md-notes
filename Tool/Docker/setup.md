# 阿里云Linux服务器安装Docker

[TOC]

## 一、使用官方安装脚本自动安装 （仅适用于公网环境）

```shell
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

## 二、安装校验

```shell
root@iZwz91577x7sn2pgjejf5kZ:~# docker version
Client: Docker Engine - Community
 Version:           20.10.12
 API version:       1.41
 Go version:        go1.16.12
 Git commit:        e91ed57
 Built:             Mon Dec 13 11:45:33 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.12
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.16.12
  Git commit:       459d0df
  Built:            Mon Dec 13 11:43:42 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.12
  GitCommit:        7b11cfaabd73bb80907dd23182b9347b4245eb5d
 runc:
  Version:          1.0.2
  GitCommit:        v1.0.2-0-g52b36a2
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

## 三、配置镜像加速器

```shell
mkdir -p /etc/docker

tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://1on9ula1.mirror.aliyuncs.com"]
}
EOF

systemctl daemon-reload
systemctl restart docker
```

## 四、配置权限

配置 Docker 的权限，使得用户可以在不使用 `sudo` 的情况下运行 Docker 命令。

```sh
sudo usermod -aG docker $USER

# -aG：组合命令，-a 表示 append，用于将用户添加到一个新的组里；-G 表示 group，用于指定组名。
# docker：这是你要将用户添加到的组的名称，在这个情况下是 docker 组。
# $USER：这是一个环境变量，代表当前登录的用户的用户名。
```

