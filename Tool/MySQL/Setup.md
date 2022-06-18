# 安装MySQL

[TOC]

## 一、使用Docker安装

### 1.1 创建容器

执行下列命令创建`MongoDB`容器：

```shell
docker run -d --restart=always -p 3306:3306 --name mysql -v /data/mysql/data:/var/lib/mysql -v /data/mysql/log:/var/log/mysql -v /data/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456 mysql
```

>   `-d` 表示后台启动
>
>   `--restart` 表示`docker`启动时自启动相关容器
>
>   `-p` 映射端口，格式为**主机端口:容器端口**
>
>   `--name` 指定容器名称
>
>   `-v` 挂载目录，格式为**主机目录:容器目录**
>
>   `-e` 设置环境变量，用于设置mysql密码

### 1.2 错误和警告

```
2022-06-18T17:51:09.385595Z 9 [Warning] [MY-010055] [Server] IP address '111.28.249.223' could not be resolved: Temporary failure in name resolution
mbind: Operation not permitted
2022-06-18T17:51:29.210852Z 10 [Warning] [MY-010055] [Server] IP address '111.28.249.223' could not be resolved: Temporary failure in name resolution
2022-06-18T17:51:32.933927Z 11 [Warning] [MY-010055] [Server] IP address '111.28.249.223' could not be resolved: Temporary failure in name resolution
2022-06-18T17:51:36.502009Z 12 [Warning] [MY-010055] [Server] IP address '111.28.249.223' could not be resolved: Temporary failure in name resolution
mbind: Operation not permitted
mbind: Operation not permitted
2022-06-18T17:57:22.133375Z 13 [Warning] [MY-010058] [Server] Hostname 'security.criminalip.com' does not resolve to '94.102.61.23'.
```

-   `Warning`解决办法参考：https://blog.csdn.net/lxpbs8851/article/details/7892256
-   `mbind: Operation not permitted`参考：https://github.com/docker-library/mysql/issues/303

