# Ubuntu下配置FTP

## 一、FTP工作原理

### 1.1  FTP工作原理介绍

FTP的工作方式的C/S模式(客户端/服务器模式)，采用TCP面向连接的可靠传输协议，利用端口20来传输数据，端口21来传输客户端和服务器之间控制命令。

### 1.2  两种模式:从服务器角度

+   **主动(PORT style)**: 服务器主动连接

    命令(控制) :客户端:随机port -服务器: 21/tcp

    数据:客户端:随机port <--服务器: 20/tcp

+   **被动(PASV style)**: 客户端主动连接

    命令(控制) :客户端:随机port ---服务器: 21/tcp

    数据:客户端:随机port ->-服务器:随机port /tcp

### 1.3  FTP服务状态码:

>   1XX:  信息                 125:  数据连接打开
>
>   2YY：成功类状态          200:  命令OK          230:登录成功
>
>   3XX ：补充类              331：用户名OK
>
>   4XX： 客户端错误         425：不能打开数据连接
>
>   5XX：服务器错误          500：不能登陆

## 二、安装

```bash
# 系统版本
# Ubuntu 18.04.6 LTS (GNU/Linux 4.15.0-48-generic x86_64)

apt update
apt install vsftpd

$ vsftpd -v
vsftpd: version 3.0.3
```

### 1.1 vsftpd允许三种认证模式登录FTP服务器：

-   **匿名模式**：**最不安全**的FTP的登录模式，只需填入anonymous用户名，按下回车就可以登录。
-   **本地用户模式**：通过linux本地操作系统的用户名密码来登录的模式，相对安全，但是如果系统的用户被截获密码泄露，攻击者可以利用该账号密码登录ftp服务器从而造成一系列破坏，可能会威胁到整个服务器。
-   **虚拟用户模式**：**推荐**的模式，其工作原理是单独设置FTP的用户文件信息，创建相关的数据库，虚拟出可以登录的FTP的用户信息，实际不存在，只能在FTP服务程序下使用。

### 1.2 配置本地用户模式

```bash
#备份配置文件
cp /etc/vsftpd.conf /etc/vsftpd.conf.bak

#修改配置文件
vim /etc/vsftpd.conf
#取消注释以下内容注释
write_enable=YES

#删除root,Ubuntu下root用户不能ftp登陆
vim /etc/ftpusers

#重启ftp服务
systemctl restart vsftpd
```

### 1.3 配置虚拟用户模式

默认缺少`db_load`，且测试环境安全性要求不高，**暂时不采用**

参考文章：

-   [关于ubuntu18.04vsftpd和ftp的搭建](https://zhuanlan.zhihu.com/p/367086919)
-   [ubuntu 18.04 搭建ftp服务器](https://blog.csdn.net/kaikai136412162/article/details/94318822)

### 1.4 登录

此时应该能够使用之前创建的jokerxin登录FTP服务器，现在可以上传和下载文件到您的内容。这些文件将保存在ftpuser用户的主目录中（/home/ftpuser）。配置文件已禁用匿名使用，因此登录的唯一方法是使用服务器上的工作帐户。

## 三、问题

1.   **Ubuntu下root用户不能ftp登陆**

     一般情况下,由于种种原因ftp是不让root用户远程登陆；但只要你修改以个文件就可以登陆了：注释掉 /etc/ftpusers 中的root，最后重启 vsftpd ( service vsftpd restart ).

2.   前 vsftpd.conf 配置:

     ```bash
     listen=NO
     listen_ipv6=YES
     anonymous_enable=NO
     local_enable=YES
     write_enable=YES
     local_umask=022
     dirmessage_enable=YES
     use_localtime=YES
     xferlog_enable=YES
     connect_from_port_20=YES
     chroot_local_user=YES
     secure_chroot_dir=/var/run/vsftpd/empty
     pam_service_name=vsftpd
     rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
     rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
     ssl_enable=NO
     pasv_enable=Yes
     pasv_min_port=10000
     pasv_max_port=10100
     allow_writeable_chroot=YES
     ```

3.   **550 Permission denied**

     因为vsftpd.conf配置文件后修改后未重启服务

     ```bash
     # 以下命令无法重新加载配置文件
     systemctl enable vsftpd
     systemctl start vsftpd
     
     # 需要执行以下命令
     systemctl restart vsftpd
     #或者
     systemctl reload vsftpd
     ```
