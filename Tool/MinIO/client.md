# MinIO Client

[TOC]

MinIO客户端mc命令行工具提供了一个现代化的选择，用以替代UNIX命令如ls、cat、cp、mirror和diff，同时支持文件系统和兼容Amazon S3的云存储服务。

命令文档：https://min.io/docs/minio/linux/reference/minio-mc.html?ref=docs

## 一、二进制文件

创建文件夹

```sh
mkdir ~/minio
```

下载二进制文件

```sh
curl https://dl.min.io/client/mc/release/linux-amd64/mc -o ~/minio/mc
```

给予执行权限

```sh
chmod +x ~/minio/mc
```

加入环境变量

```sh
export PATH=$PATH:~/minio
```

在Web控制台生成秘钥，然后测试，相关文件保存在`~/.mc`

```sh
mc alias set minio http://127.0.0.1:9000 ACCESS_KEY SECRET_KEY
mc admin info minio

➜  mc admin info minio                                                                           
●  127.0.0.1:9000
   Uptime: 23 hours
   Version: 2024-01-29T03:56:32Z
   Network: 1/1 OK
   Drives: 1/1 OK
   Pool: 1

Pools:
   1st, Erasure sets: 1, Drives per erasure set: 1

0 B Used, 1 Bucket, 0 Objects
1 drive online, 0 drives offline
```

## 二、使用Docker (测试无效)

官方文档：https://hub.docker.com/r/bitnami/minio-client

创建`docker`网络

```
sudo docker network create minio-net --driver bridge
```

将服务器加入新建网络

```
sudo docker network connect minio-net minio-server
```

运行容器，`--rm`表示运行后立即清除

查看服务端信息

```sh
sudo docker run --rm                                \
    -e MINIO_SERVER_HOST="http://minio-server:9000" \
    -e MINIO_SERVER_ACCESS_KEY="ACCESS_KEY"         \
    -e MINIO_SERVER_SECRET_KEY="SECRET_KEY"         \
    --network minio-net                             \
    --name minio-client                             \
    bitnami/minio-client                            \
    mc admin info minio

# 命令运行失败，没有配置别名，即远程服务端
 15:06:18.00 INFO  ==>
 15:06:18.00 INFO  ==> Welcome to the Bitnami minio-client container
 15:06:18.00 INFO  ==> Subscribe to project updates by watching https://github.com/bitnami/containers
 15:06:18.00 INFO  ==> Submit issues and feature requests at https://github.com/bitnami/containers/issues
 15:06:18.00 INFO  ==>

mc: <ERROR> Unable to initialize admin connection. No valid configuration found for 'minio' host alias.
```

下面命令查看可用的远程服务端

```sh
 sudo docker run --rm                                \
    -e MINIO_SERVER_HOST="http://minio-server:9000" \
    -e MINIO_SERVER_ACCESS_KEY="ACCESS_KEY"         \
    -e MINIO_SERVER_SECRET_KEY="SECRET_KEY"         \
    --network minio-net                             \
    --name minio-client                             \
    bitnami/minio-client                            \
    mc alias ls
    
# 以下输出应该可以说明，不存在正确配置的 minio-server
 15:00:10.27 INFO  ==>
 15:00:10.27 INFO  ==> Welcome to the Bitnami minio-client container
 15:00:10.27 INFO  ==> Subscribe to project updates by watching https://github.com/bitnami/containers
 15:00:10.27 INFO  ==> Submit issues and feature requests at https://github.com/bitnami/containers/issues
 15:00:10.27 INFO  ==>

gcs
  URL       : https://storage.googleapis.com
  AccessKey : YOUR-ACCESS-KEY-HERE
  SecretKey : YOUR-SECRET-KEY-HERE
  API       : S3v2
  Path      : dns
local
  URL       : http://localhost:9000
  AccessKey :
  SecretKey :
  API       :
  Path      : auto
play
  URL       : https://play.min.io
  AccessKey : Q3AM3UQ867SPQQA43P2F
  SecretKey : zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG
  API       : S3v4
  Path      : auto
s3
  URL       : https://s3.amazonaws.com
  AccessKey : YOUR-ACCESS-KEY-HERE
  SecretKey : YOUR-SECRET-KEY-HERE
  API       : S3v4
  Path      : dns
```

