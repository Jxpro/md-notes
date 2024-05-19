# 安装MySQL

[TOC]

## 一、使用Docker安装

### 1.1 创建容器

执行下列命令创建`MySQL`容器：

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

## 二、Linux原生安装

相关讨论，使用docker容器运行数据库是否合适：

1.   https://www.zhihu.com/question/42976239
2.   https://www.zhihu.com/question/627105598

### 1.1 安装MySQL

```shell
sudo apt update

# 安装最新版本
sudo apt install -y mysql-server
# 安装指定版本
sudo apt install -y mysql-server-8.0
```

### 1.2 设置密码

```shell
# 直接登录mysql，默认安装时没有设置密码
sudo mysql -u root
# 设置新密码 mysql 8.0
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
```

### 1.3 注意的问题

>   Skipping password set for root as authentication with auth_socket is used by default. If you would like to use password authentication instead, this can be done with the "ALTER_USER" command. See https://dev.mysql.com/doc/refman/8.0/en/alter-user.html#alter-user-password-management for more information.

上面文档的意思是：在`MySQL 8`中，默认情况下，`root`用户可能使用的是基于`auth_socket`的认证插件，这种方式允许系统用户通过操作系统的用户凭证直接登录`MySQL`，而不需要密码。

所以在使用`sudo apt install -y mysql-server`安装完`MySQL `后，需要使用`sudo mysql -u root`登录数据库并修改`root`密码，而不能使用`mysql -u root`，注意`sudo`不能省略！！！
