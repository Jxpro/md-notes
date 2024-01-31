

# MinIO服务端配置

[TOC]

`MinIO`是一款高性能对象存储软件，采用GNU Affero通用公共许可证第3.0版发布，兼容亚马逊S3云存储服务，主要用于存储非结构化数据（如照片、视频、日志文件等）。下面是 Single-Node Single-Drive (SNSD) 方式的配置，即单节点且仅部署在一块硬盘上。

## 一、Docker安装

官方文档：https://min.io/docs/minio/container/index.html，官方教程忘记指定启动目录了。

官方有三个镜像，感觉比较混乱：[bitnami/minio](https://hub.docker.com/r/bitnami/minio)、[quay.io/minio/minio](https://quay.io/repository/minio/minio)、[minio/minio](https://hub.docker.com/r/minio/minio)。

`bitnami/minio`镜像的启动方式和`quay.io/minio/minio`及`minio/minio`不太一样，`quay.io/minio/minio`及`minio/minio`是同一个镜像。官方教程使用的是后者，本人目前使用的是前者。

### 1.1 创建映射目录


```sh
mkdir -p ~/minio/data

# 如果报错，则需要授予权限，其中1001:1001是许多Bitnami容器使用的默认非root用户ID
# /bitnami/minio/data/.root_user: Permission denied
sudo chmod -R 755 minio/data
sudo chown -R 1001:1001 minio/data
```

### 1.2 运行MinIO镜像

```sh
# bitnami/minio
sudo docker run -d                         \
  -p 9000:9000 -p 9001:9001                \
  -v ~/minio/data:/bitnami/minio/data      \
  -e MINIO_ROOT_USER="minio-root-user"     \
  -e MINIO_ROOT_PASSWORD="root-password"   \
  --name "minio-server"                    \
  --restart always                         \
  bitnami/minio

# minio/minio
docker run -d                              \
  -p 9000:9000 -p 9001:9001                \
  -v ~/minio/data:/data                    \
  -e MINIO_ROOT_USER="minio-root-user"     \
  -e MINIO_ROOT_PASSWORD="root-password"   \
  --name "minio-server"                    \
  --restart always                         \
  minio/minio server /data --console-address ":9001"
  
# quay.io/minio/minio
docker run -d                              \
  -p 9000:9000 -p 9001:9001                \
  -v ~/minio/data:/data                    \
  -e MINIO_ROOT_USER="minio-root-user"     \
  -e MINIO_ROOT_PASSWORD="root-password"   \
  --name "minio-server"                    \
  --restart always                         \
  quay.io/minio/minio server /data --console-address ":9001"
```

### 1.3 访问Web控制台

访问http://ip:port，本地直接使用 http://localhost:9001即可

![image-20240131222509183](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2024/01-31-222510.png)

## 二、Linux原生安装

官方文档：https://min.io/docs/minio/linux/index.html

### 2.1 安装MinIO

Ubuntu：

```sh
wget https://dl.min.io/server/minio/release/linux-amd64/archive/minio_20240129035632.0.0_amd64.deb -O minio.deb

sudo dpkg -i minio.deb
```

CentOS：

```sh
wget https://dl.min.io/server/minio/release/linux-amd64/archive/minio-20240129035632.0.0-1.x86_64.rpm -O minio.rpm
sudo dnf install minio.rpm
```

### 2.2 配置systemd文件

>   `.deb` 或 `.rpm` 软件包将以下 systemd 服务文件安装到 `/usr/lib/systemd/system/minio.service` 。

```sh
[Unit]
Description=MinIO
Documentation=https://min.io/docs/minio/linux/index.html
Wants=network-online.target
After=network-online.target
AssertFileIsExecutable=/usr/local/bin/minio

[Service]
WorkingDirectory=/usr/local

User=minio-user
Group=minio-user
ProtectProc=invisible

# - 符号具有特殊含义。它指示systemd在读取环境文件时，如果该文件不存在，则不认为这是一个错误。
EnvironmentFile=-/etc/default/minio
ExecStartPre=/bin/bash -c "if [ -z \"${MINIO_VOLUMES}\" ]; then echo \"Variable MINIO_VOLUMES not set in /etc/default/minio\"; exit 1; fi"
ExecStart=/usr/local/bin/minio server $MINIO_OPTS $MINIO_VOLUMES

# MinIO RELEASE.2023-05-04T21-44-30Z adds support for Type=notify (https://www.freedesktop.org/software/systemd/man/systemd.service.html#Type=)
# This may improve systemctl setups where other services use `After=minio.server`
# Uncomment the line to enable the functionality
# Type=notify

# Let systemd restart this service always
Restart=always

# Specifies the maximum file descriptor number that can be opened by this process
LimitNOFILE=65536

# Specifies the maximum number of threads this process can create
TasksMax=infinity

# Disable timeout logic and wait until process is stopped
TimeoutStopSec=infinity
SendSIGKILL=no

[Install]
WantedBy=multi-user.target

# Built for ${project.name}-${project.version} (${project.name})
```

### 2.3 配置环境变量

>   在 `/etc/default/minio` 处创建环境变量文件。
>
>   ```sh
>   vim /etc/default/minio
>   ```

```sh
# MINIO_ROOT_USER and MINIO_ROOT_PASSWORD sets the root account for the MinIO server.
# This user has unrestricted permissions to perform S3 and administrative API operations on any resource in the deployment.
# Omit to use the default values 'minioadmin:minioadmin'.
# MinIO recommends setting non-default values as a best practice, regardless of environment

MINIO_ROOT_USER=myminioadmin
MINIO_ROOT_PASSWORD=minio-secret-key-change-me

# MINIO_VOLUMES sets the storage volume or path to use for the MinIO server.

MINIO_VOLUMES="/mnt/data"

# MINIO_OPTS sets any additional commandline options to pass to the MinIO server.
# For example, `--console-address :9001` sets the MinIO Console listen port
MINIO_OPTS="--console-address :9001"

# MINIO_SERVER_URL sets the hostname of the local machine for use with the MinIO Server
# MinIO assumes your network control plane can correctly resolve this hostname to the local machine

# Uncomment the following line and replace the value with the correct hostname for the local machine and port for the MinIO server (9000 by default).

#MINIO_SERVER_URL="http://minio.example.net:9000"
```

### 2.4 创建用户和目录

下面命令创建的用户似乎存在问题，即使将`minio.service`涉及到的所有文件都加入`chown`命令也无法启动服务。

```sh
groupadd -r minio-user
# -r 参数通常用于创建系统用户组，这种用户组不是为了登录系统的，而是为了运行系统服务。
# minio-users 是想创建的用户组的名字。

useradd -M -r -g minio-user minio-user
# -M 参数表示不要自动创建用户的家目录。
# -r 参数（跟 groupadd 里的 -r 类似）表示创建一个系统用户，用于运行服务，而不是登录系统。
# -g minio-users 指定这个新用户的初始登录组（或主用户组）为 minio-users。
# 最后的 minio-user 是新创建的用户名。
# 具体作用是创建一个不用于登录的系统用户，并将其加入到 minio-users 组中，且不自动为其创建家目录。

mkdir /mnt/data
chown minio-user:minio-user /mnt/data /etc/default/minio
```

### 2.5 启动服务

```sh
# 启动服务
sudo systemctl start minio.service

# 查看状态
sudo systemctl status minio.service

# 开机自启
sudo systemctl enable minio.service
```

### 2.6 遇到的问题

按照官方所给教程，创建的系统服务用户，即使使用`chown`命令将`minio.service`中涉及到的所有文件的所有权都变为`minio-user:minio-users`，服务也会启动失败：

```sh
× minio.service - MinIO
     Loaded: loaded (/lib/systemd/system/minio.service; disabled; vendor preset: enabled)
     Active: failed (Result: exit-code) since Tue 2024-01-30 19:03:00 CST; 7s ago
       Docs: https://docs.min.io
    Process: 34606 ExecStartPre=/bin/bash -c if [ -z "${MINIO_VOLUMES}" ]; then echo "Variable MINIO_VOLUMES not set in>
    Process: 34607 ExecStart=/usr/local/bin/minio server $MINIO_OPTS $MINIO_VOLUMES (code=exited, status=1/FAILURE)
   Main PID: 34607 (code=exited, status=1/FAILURE)
        CPU: 247ms
        
Jan 30 19:03:09 iZbp1dul7chbryf0g1wjb3Z systemd[1]: minio.service: Scheduled restart jo>
Jan 30 19:03:09 iZbp1dul7chbryf0g1wjb3Z systemd[1]: Stopped MinIO.
Jan 30 19:03:09 iZbp1dul7chbryf0g1wjb3Z systemd[1]: minio.service: Start request repeat>
Jan 30 19:03:09 iZbp1dul7chbryf0g1wjb3Z systemd[1]: minio.service: Failed with result '>
Jan 30 19:03:09 iZbp1dul7chbryf0g1wjb3Z systemd[1]: Failed to start MinIO.
```

将`/usr/lib/systemd/system/minio.service`中的`User`和`Group`字段去掉，以默认或当前用户可以运行成功，但是有点违背官方推荐的最小权限原则。

### 2.7 解决的办法

错误的原因是因为指定了`MINIO_VOLUMES="/root/minio/data"`，即使用下面命令将所有权都改变

```sh
chown minio-user:minio-user /root/minio /root/minio/data /usr/local /etc/default/minio /usr/local/bin/minio
```

但是`/root`目录本身的权限就是就是`drwx------`，其他用户根本不可读，所以会访问失败，导致服务无法启动，具体使用`journalctl -u minio.service -b 0`查看全部日志，可以发现：

```sh
Jan 30 19:07:09 iZbp1dul7chbryf0g1wjb3Z systemd[1]: Starting MinIO...
Jan 30 19:07:09 iZbp1dul7chbryf0g1wjb3Z minio[34896]: ERROR Unable to use the drive /root/minio/data: lstat /root/minio>
Jan 30 19:07:09 iZbp1dul7chbryf0g1wjb3Z systemd[1]: minio.service: Main process exited, code=exited, status=1/FAILURE
Jan 30 19:07:09 iZbp1dul7chbryf0g1wjb3Z systemd[1]: minio.service: Failed with result 'exit-code'.
Jan 30 19:07:09 iZbp1dul7chbryf0g1wjb3Z systemd[1]: Failed to start MinIO.
Jan 30 19:07:09 iZbp1dul7chbryf0g1wjb3Z systemd[1]: minio.service: Scheduled restart jo>
Jan 30 19:07:09 iZbp1dul7chbryf0g1wjb3Z systemd[1]: Stopped MinIO.
Jan 30 19:07:09 iZbp1dul7chbryf0g1wjb3Z systemd[1]: minio.service: Start request repeat>
Jan 30 19:07:09 iZbp1dul7chbryf0g1wjb3Z systemd[1]: minio.service: Failed with result '>
Jan 30 19:07:09 iZbp1dul7chbryf0g1wjb3Z systemd[1]: Failed to start MinIO.
```

但是`/mnt`目录的权限是`drwxr-xr-x`，所以可以在此目录下创建`minio`目录并设置所有权

```sh
chown minio-user:minio-user /mnt/minio /etc/default/minio
```

>   Linux系统中的`/mnt`目录通常用于临时挂载文件系统。在`/mnt`目录下创建文件或子目录是可以的，但通常不推荐这样做，因为可能会引起混淆，因为这些位置通常预期只包含挂载的文件系统的内容。
>
>   `/mnt`目录下的文件不会因为系统重启而消失，除非它们位于一个临时挂载的文件系统中。`/mnt`本身只是一个普通目录，其行为与Linux文件系统中的其他目录相同，即在文件系统层面上是持久的。
>
>   如果你在`/mnt`下挂载了外部存储设备（如U盘、移动硬盘等），并在该挂载点中存储了文件，那么：
>
>   1.  **在外部存储设备上**：文件实际上是存储在外部设备上的，重启不会影响这些文件，除非在重启前它们没有被正确卸载，可能会导致数据丢失或损坏。
>   2.  **在本地文件系统上**：如果你在挂载点创建了文件或目录（在没有任何东西被挂载到该点时），这些文件或目录实际上是存储在本地文件系统上的。当挂载点被占用时（例如，当你挂载了一个外部存储设备时），这些文件或目录会被暂时“隐藏”，直到挂载的文件系统被卸载。在这种情况下，这些文件或目录也是持久的，重启后仍然存在，只要外部存储设备没有被挂载到同一挂载点。

然后可以正常启动服务了

```sh
● minio.service - MinIO
     Loaded: loaded (/lib/systemd/system/minio.service; disabled; vendor preset: enabled)
     Active: active (running) since Wed 2024-01-31 21:37:24 CST; 8min ago
       Docs: https://docs.min.io
    Process: 62555 ExecStartPre=/bin/bash -c if [ -z "${MINIO_VOLUMES}" ]; then echo "Variable MINIO_VOLUMES not set in>
   Main PID: 62556 (minio)
      Tasks: 8
        CPU: 805ms
     CGroup: /system.slice/minio.service
             └─62556 /usr/local/bin/minio server --console-address :9001 /mnt/data
```

