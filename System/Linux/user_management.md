# Linux下的用户管理

[TOC]

## 一、groupdd & addgroup

-   **groupadd**：
    1.   通用的命令，它是大多数Linux发行版中的标准工具。
    2.   提供了一系列的选项来配置新的用户组，比如设置组ID (`-g`) 或者指定组的其他属性。

-   **addgroup**：
    1.   与Debian及其派生系统（如Ubuntu）关联，作为用户友好的前端。
    2.   主要功能相同，可能提供了一些额外的选项和用户友好的特性，比如更直观的提示。

举例：

```sh
# 列出所有组
cat /etc/group

addgroup common

groupadd -r minio-users

# -r 参数通常用于创建系统用户组，这种用户组不是为了登录系统的，而是为了运行系统服务。
# minio-users 是想创建的用户组的名字。
```

## 二、useradd & adduser

-   **useradd**
    1.   是一个低级别的命令，通常由系统管理员使用，是Linux系统自带的标准命令。
    2.   功能强大，但需要使用选项设置用户的很多信息，比如密码、用户目录等。
    3.   如果不指定具体选项（例如家目录和登陆shell等）就不会自动为用户设置，需要手动配置。
    4.   适用于对于经验丰富的管理员，希望通过脚本或是精确控制用户账户设置。
-   adduser
    1.   通常是一个脚本，它在 `useradd` 的基础上添加了一些额外的功能，使得创建新用户的过程更为友好和简单。`adduser` 可能不是所有Linux发行版都有，主要在基于Debian的系统中使用（比如Ubuntu）。
    2.   通常会交互式引导用户创建新账户，比如询问密码、家目录等信息，更为简单直观。
    3.   会自动为用户设置一些默认值，还会询问是否设置密码、是否复制相关文件到家目录等。
    4.   适用于希望快速方便地创建用户，并且不需要太多自定义设置的普通用户或管理员。

举例：

```sh
# 列出所有用户
cat /etc/passwd

adduser --ingroup common xin

useradd -M -r -g minio-users minio-user

# -M 参数表示不要自动创建用户的家目录。
# -r 参数（跟 groupadd 里的 -r 类似）表示创建一个系统用户，用于运行服务，而不是登录系统。
# -g minio-users 指定这个新用户的初始登录组（或主用户组）为 minio-users。
# 最后的 minio-user 是新创建的用户名。
# 具体作用是创建一个不用于登录的系统用户，并将其加入到 minio-users 组中，且不自动为其创建家目录。
```

## 三、userdel & deluser

1.  **userdel**:
    -   是标准的 Linux 命令，定义在 Linux 用户管理工具集中。
    -   行为相对基础和严格，直接对 `/etc/passwd`、`/etc/shadow`、`/etc/group` 等文件进行操作。
    -   默认情况下不会删除用户的主目录和邮件存储箱。如果想要删除，需要手动添加 `-r` 选项。
2.  **deluser**:
    -   是一个 Perl 脚本，更多地被集成在 Debian 及其派生系统中。
    -   提供了更多的功能和选项，比如处理用户组，以及使用`--remove-all-files`删除所有文件。
    -   默认会删除用户的主目录和邮件存储箱，也可以添加选项来防止这种行为。

举例：

```sh
userdel -r foo
# 删除foo及其家目录。

deluser --remove-all-files bar
# 删除bar并删除用户的所有文件
```

>   慎用 --remove-all-files，本人使用chown将bash所有权改变后，使用该选项误删了bash。。。

## 四、usermod

用于修改已存在用户的配置信息。这个命令功能非常强大，可以处理各种与用户账户相关的设置，比如：

1.  **更改用户名**：可以通过 `-l` 选项更改用户的登录名。
2.  **添加用户到组**：通过 `-aG`（append to group）选项可以将用户添加到一个或多个附加组中，而不会影响用户所属的其他组。
3.  **更改用户主目录**：使用 `-d` 选项和 `-m` 选项，可以移动用户的主目录到新位置。
4.  **更改用户的登录shell**：`-s` 选项允许改变用户的默认登录 shell。
5.  **更改用户的UID（用户ID）**：`-u` 选项用于修改用户的 UID。
6.  **更改用户的GID（用户主组ID）**：`-g` 选项用于修改用户的主组ID。
7.  **锁定和解锁用户账户**：`-L` 选项用于锁定账户，防止其登录；`-U` 选项用于解锁账户。

举例：

```sh
usermod -aG docker $USER

# -aG：组合命令，-a 表示 append，用于将用户添加到一个新的组里；-G 表示 group，用于指定组名。
# docker：这是你要将用户添加到的组的名称，在这个情况下是 docker 组。
# $USER：这是一个环境变量，代表当前登录的用户的用户名。
```

## 五、sudo privilege

打开相关配置文件

```shell
# 使用 nano 编辑文件
# 直接编辑，完成后按下 Ctrl+O 保存文件，直接 Enter 保存更改到当前文件
# 保存文件后，按下 Ctrl+X 退出
sudo visudo

# visudo 实际编辑了 /etc/sudoers 文件，可以直接使用 vi/vim 编辑
sudo vim /etc/sudoers
```

找到下面一行，进行复制

```shell
root  ALL=(ALL)    ALL
```

粘贴到下一行，并将 `root` 修改为 `$user` 对应的用户名

```shell
user  ALL=(ALL)    ALL
```

