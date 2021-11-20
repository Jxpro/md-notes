# Nodejs升级方法

## 方法一： 用n模块升级nodejs（Linux）

注意：此方法**仅适用于Linux**，Windows请使用其他方法

安装

```bash
# 全局安装
npm install -g n
```

查看本地安装的所有版本，有可选参数available，显示所有可下载的版本

```bash
# 查看版本
nvm list available
```

切换版本

```bash
# 升级到最新稳定版
n stable

# 升级到最新版
n latest

# 切换使用版本
n 7.10.0
```

删除指定版本

```bash
n rm 7.10.0
```

用指定的版本执行脚本

```bash
n use 7.10.0 app.js
```

## 方法二：用NVM来升级nodejs

安装：[Linux](https://github.com/nvm-sh/nvm/releases) | [Windows](https://github.com/coreybutler/nvm-windows/releases)

下载对应版本nodejs

```bash
$ nvm use 16
Now using node v16.9.1 (npm v7.21.1)

$ nvm use 14
Now using node v14.18.1 (npm v6.14.15)

$ nvm install 14.7.0

# "node" is an alias for the latest version
$ nvm install node
```

切换版本

```bash
nvm use 14.18.1
```

删除指定版本

```bash
nvm uninstall 14.18.1
```

常用命令

>   -   `nvm arch [32|64]`： 显示node是运行在32位还是64位模式。指定32或64来覆盖默认体系结构。
>       -`nvm install <version> [arch]`：该可以是node.js版本或最新稳定版本latest。（可选[arch]）指定安装32位或64位版本（默认为系统arch）。设置[arch]为all以安装32和64位版本。在命令后面添加–insecure，可以绕过远端下载服务器的SSL验证。
>   -   `nvm list [available]`：列出已经安装的node.js版本。可选的available，显示可下载版本的部分列表。这个命令可以简写为nvm ls [available]。
>   -   `nvm on`： 启用node.js版本管理。
>   -   `nvm off`： 禁用node.js版本管理(不卸载任何东西)
>   -   `nvm proxy [url]`： 设置用于下载的代理。留[url]空白，以查看当前的代理。设置[url]为none删除代理。
>   -   `nvm node_mirror [url]`：设置node镜像，默认为https://nodejs.org/dist/.。可以设置为淘宝的镜像https://npm.taobao.org/mirrors/node/
>   -   `nvm npm_mirror [url]`：设置npm镜像，默认为https://github.com/npm/npm/archive/。可以设置为淘宝的镜像https://npm.taobao.org/mirrors/npm/
>   -   `nvm uninstall <version>`： 卸载指定版本的nodejs。
>   -   `nvm use [version] [arch]`： 切换到使用指定的nodejs版本。可以指定32/64位[arch]。
>       -`nvm use <arch>`：将继续使用所选版本，但根据提供的值切换到32/64位模式
>   -   `nvm root [path]`： 设置 nvm 存储node.js不同版本的目录 ,如果未设置，将使用当前目录。
>       -`nvm version`： 显示当前运行的nvm版本，可以简写为`nvm v`

## 三、官网下载覆盖安装

下载：[最新版](https://nodejs.org/en/download/) | [旧版](https://nodejs.org/en/download/releases/)

**注意问题**：之前使用`npm install npm -g`升级过`npm`的话，`npm`会保持之前的版本不变（及时版本比较旧），需要执行``npm uninstall npm -g`卸载之前安装的版本，才会变成`nodejs`自带的`npm`版本

```bash
# 当前nodejs版本，默认npm版本为8.1.0
$ node -v
v16.13.0

# 此时仍是旧版本
$ npm -v
8.0.0

# 执行卸载命令
$ npm uninstall npm -g

# 变为默认npm版本
$ npm -v
8.1.0
```



