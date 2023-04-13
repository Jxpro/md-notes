# iTerm2通过ssh连接服务器并传输文件

[TOC]

## 一、手动登录服务器

```shell
ssh user@host
user@host's password:
```

整个 ssh 密码登录过程是这样的：

1.   用户向远程主机发登录请求：ssh user@host
2.   远程主机收到用户的登录请求，把自己的公钥发给用户
3.   用户使用这个公钥，将登录密码加密后，发送回远程主机
4.   远程主机用自己的私钥，解密登录密码，如果密码正确，就同意用户登录。

## 二、自动登录服务器

iTerm2 有一个非常强大的功能叫触发器（Trigger），**触发器是用户可配置的正则表达式，当终端会话接收到与正则表达式相匹配的文本时，会执行相关的操作**。这里的操作包括突出显示匹配的文本，显示警报，发回文本等等。

### 2.1 自动请求连接

>   你可以直接在或者~/.zshrc里面添加一个alias，方便任何时候在当前窗口连接服务器（推荐）
>
>   ```shell
>   alias server="ssh user@host"
>   ```
>
>   还可以通过下面介绍的方法，在一个新的会话窗口来连接服务器。

1.   首先点击`Preference - Profiles`，新建`Profiles`。
2.   然后点击`Login Shell`，改为`Command`。
3.   输入`ssh user@host`，填写服务器的 ip 和用户名，如果端口不是 22 还要通过 -p 指定端口。

![image-20230319020250078](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-19-024028.png)这样当你在菜单栏点击`Profiles`，选择`server`打开新窗口时，即可自动请求连接。

![image-20230319024806350](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-19-024806.png)

### 2.2 自动输入密码

在当前`Profiles`中，切换到`Advanced`，找到`Trriggers`，点击`edit`

![image-20230319025638608](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-19-025639.png)

1.   首先，点击`+`新建一个触发器。
2.   在`Regular Eexpression`中，填写`root@xxx.xxx.xxx.xxx's password`，表示出现这行字符串时触发。
3.   在`Action`中选择`Send Text`，表示自动输入文本内容。
4.   在`Parameters`中填写你的密码，最后增加一个 `\n` ，表示回车。
5.   最后，把 `Instant` 的复选框选中并`close`。

![image-20230319030037433](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-19-030037.png)

这样便实现了自动输入密码

### 2.3 其他一些方法

[expect脚本和sshpass指令连接](https://blog.csdn.net/OMars/article/details/89627642)

>   用expect的方式登录服务器，用rz、sz进行相关操作时，显示都是没问题的，可以正常选择上传文件或者选择下载目录，没有抛出任何异常但是最后上传文件也找不到不过文件却不能成功上传下载。
>
>   解决方法参考（未尝试）：[iTerm2 实现 ssh 自动登录，并使用 Zmodem 实现快速传输文件](https://icloudnative.io/posts/iterm2-auto-login/)

## 三、通过lrzsz传输文件

### 3.1 什么是 Zmodem

`Zmodem`是针对`modem`的一种支持错误校验的文件传输协议。`Zmodem`是`Ymodem`的改进版，后者又是`Xmodem`的改进版。`Zmodem`不仅能传输更大的数据，而且错误率更小。

利用`Zmodem`协议，可以在`modem`上发送 512 字节的数据块。`Zmodem`包含一种名为检查点重启的特性，如果通信链接在数据传输过程中中断，能从断点处而不是从开始处恢复传输。

`lrzsz`软件包是支持`Zmodem`协议的工具包。 其包含的`rz`、`sz`命令是通过`ZModem`协议在远程服务器和终端机器间上传下载文件的利器。

### 3.2 服务端配置

为了正确通过`sz`、`rz`命令传输文件，服务端需要安装`lrzsz`软件包的。

-   Ubuntu 或 Debian

```shell
apt-get install lrzsz
```

-   RHEL 或 CentOS

```shell
yum install lrzsz
```

### 3.3 客户端（iterm2）配置

和服务器端一样，客户端同样需要安装`lrzsz`软件包，可以通过`Homebrew` 进行安装

```shell
brew install lrzsz
```

下载配置脚本，让 `iTerm2` 根据终端上显示的字符通过指定的触发器调用相应的发送和接收脚本。

脚本可存储在任意位置，建议在`~/.ssh`或者`/usr/local/bin`下，下面以`~/.ssh`为例

```shell
curl https://gist.githubusercontent.com/Jxpro/03cdd6d527ab0a88ae55f1425ff1f683/raw/5b374789be149f2a44dfd256ce316f2274d175ce/iterm2-zmodem.sh -o ~/.ssh/iterm2-zmodem.sh
```

添加执行权限

```shell
chmod +x ~/.ssh/iterm2-zmodem.sh
```

配置触发器（上文自动登录中所介绍），按如下内容添加两个触发器。

首先是 `sz` 指令的触发器（`Parameters`中需确认上述脚本所在位置）

```bash
Regular expression: rz waiting to receive.\*\*B0100
Action: Run Silent Coprocess
Parameters: ~/.ssh/iterm2-zmodem.sh send
Instant: checked
```

其次是 `rz` 指令的触发器（`Parameters`中需确认上述脚本所在位置）

```bash
Regular expression: \*\*B00000000000000
Action: Run Silent Coprocess
Parameters: ~/.ssh/iterm2-zmodem.sh recv
Instant: checked
```

成功增加完成后如图所示

![image-20230319032857989](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-19-032858.png)

### 3.4 发送文件到远端服务器

-   在远端服务器执行`rz`命令
-   本地选择文件传输
-   等待传输指示消失

### 3.5 接收远端服务器的文件

-   在远端服务器执行`sz filename1 filename2 … filenameN`命令
-   本地选择目录保存
-   等待传输指示消失
