# .bashrc与.profile等的区别

## 一、本机测试

-   系统版本：Ubuntu 18.04.6 LTS (GNU/Linux 4.15.0-48-generic x86_64)

1.   **root用户**登录时

     ```bash
     Welcome to Alibaba Cloud Elastic Compute Service !
     
     Last login: Fri Oct 29 17:34:21 2021 from 223.104.98.53
     exec /etc/profile
     exec /etc/bash.bashrc
     exec ~/.bashrc
     exec ~/.profile
     root@iZwz91577x7sn2pgjejf5kZ:~# 
     ```

2.   **普通用户[^1]**登录时：

     ```bash
     Welcome to Alibaba Cloud Elastic Compute Service !
     
     Last login: Fri Oct 29 17:28:52 2021 from 223.104.98.53
     exec /etc/profile
     $ 
     ```

## 二、资料查询

1.   **交互式 shell** ( login shell ) 和**非交互式 shell** ( non-login shell )

     -   交互式模式：shell等待你的输入，并且执行你提交的命令，这种模式也是大多数用户非常熟悉的：登录、执行一些命令、签退。当你签退后，shell也终止了。 

     -   非交互式模式：在这种模式下，shell不与你进行交互，而是读取存放在文件中的命令,并且执行它们。当它读到文件的结尾，shell也就终止了。

2.   配置文件有两个级别，**系统级**(/etc)和**用户级**(~/)，每次调用优先调用系统级

     -   `/etc/profile`，`/etc/bashrc`是系统全局环境变量设定

     -   `~/.profile`，`~/.bashrc`用户家目录下的私有环境变量设定
     -   `.bashrc`用于non-login shell，`.profile`用于login shell

3.   网友总结的执行过程 ( [关于“.bash_profile”和“.bashrc”区别的总结](https://blog.csdn.net/duzilonglove/article/details/79729840) )

     >    登陆过程：
     >
     >   1.   读取并执行 /etc/profile 文件
     >   2.   读取并执行~/.bash_profile文件
     >        -   若文件不存在，则读取并执行~/.bash_login文件；
     >        -   若文件不存在，则读取并执行~/.profile文件；
     >
     >    交互式登出过程 ( 非交互式无 ) ：
     >
     >   1.   读取并执行~/.bash_logout文件；
     >   2.   读取并执行/etc/bash.bash_logout文件；



[^1]: root 用户通过useradd -m 添加的用户

