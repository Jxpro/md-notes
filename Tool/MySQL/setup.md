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
# 如果当前为普通用户，则 sudo 不能省略！！！具体见 mysql_secure_installation 中关于root密码的说明
sudo mysql -u root
# 为 mysql 8.0 设置新密码，其实也可以跳过
# 因为后面 mysql_secure_installation 可以确保 root 用户只能在本机登录，而在本机通过 auth_socket 认证插件，可以直接使用 sudo 登录而不需要输密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
```

### 1.3 安全检查

MySQL安装附带了一个名为`mysql_secure_installation`的程序，可以快速安全性，我们直接运行

```shell
sudo mysql_secure_installation
```

输入和选项如下：

```shell
Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

######### 选项1 #########
VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No: y

######### 选项2#########
There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 0

######### 如果设置了root密码，则会给出是否修改root密码的选项 #########
Using existing password for root.

Estimated strength of the password: 50
Change the password for root ? (Press y|Y for Yes, any other key for No) : n

######### 如果没有设置root密码，则会跳过下面步骤并给出提示，意思是：在MySQL 8中，默认情况下，root用户可能使用的是基于auth_socket的认证插件，这种方式允许系统用户通过操作系统的用户凭证直接登录MySQL，而不需要密码。#########
Skipping password set for root as authentication with auth_socket is used by default.
If you would like to use password authentication instead, this can be done with the "ALTER_USER" command.
See https://dev.mysql.com/doc/refman/8.0/en/alter-user.html#alter-user-password-management for more information.

######### 选项3 #########
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.

######### 选项4 #########
Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.

######### 选项5 #########
By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

######### 选项6 #########
Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done!
```

### 1.4 配置修改

打开配置文件：

```shell
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

修改默认端口号，找到`[mysqld]`下的`port`，修改为自定义的端口号，注意打开防火墙端口：

```ini
[mysqld]
#
# * Basic Settings
#
user            = mysql
# pid-file      = /var/run/mysqld/mysqld.pid
# socket        = /var/run/mysqld/mysqld.sock
port            = 12136
# datadir       = /var/lib/mysql
```

修改监听的`IP`地址，找到`[mysqld]`下的`bind-address `，修改为`0.0.0.0`，监听所有`IP`：

```ini
[mysqld]
...
# If MySQL is running as a replication slave, this should be
# changed. Ref https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_tmpdir
# tmpdir                = /tmp
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address            = 0.0.0.0
mysqlx-bind-address     = 127.0.0.1
```

### 1.5 创建新用户

首先使用`root`登录到`mysql`

```shell
sudo mysql -u root -p
```

然后使用下面`sql`语句创建用户

```sql
CREATE USER 'your_username'@'%' IDENTIFIED BY 'your_password';
```

最后赋予相应的权限

```sql
-- 授予全部权限
-- *.*表示对所有数据库和所有表的权限。
-- WITH GRANT OPTION允许该用户将其权限授予其他用户。
GRANT ALL PRIVILEGES ON *.* TO 'your_username'@'%' WITH GRANT OPTION;

-- 赋予某个数据库上的大部分权限
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER ON your_database_name.* TO 'your_username'@'%';
```

执行以下命令来刷新权限设置，确保更改立即生效：

```sql
FLUSH PRIVILEGES;
```
