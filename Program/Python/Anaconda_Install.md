# Ubuntu安装Anaconda

## 一、下载安装脚本

```bash
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2021.11-Linux-x86_64.sh
```

## 二、执行安装脚本

```bash
sh Anaconda3-2021.11-Linux-x86_64.sh
```

安装完后重启`terminal`即可使用`conda`命令

## 三、修改（清华）镜像源

修改conda镜像源，`vim ~/.condarc`打开配置文件加入以下内容：

```yaml
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch-lts: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```

清除索引缓存：

```bash
conda clean -i
```

修改pip镜像：

```bash
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

## 四、修改环境

```bash
# 关闭自动激活base环境
conda config --set auto_activate_base false

# 修改base环境Python版本,测试无效
conda install python=3.6

# 创建py2和py3环境
conda create -n py3 python=3.7
conda create -n py2 python=2

# 清楚多余的包
conda clean -y --all
```

## 五、安装过程报错

报错内容：Anaconda3-2021.11-Linux-x86_64.sh: Syntax error: "(" unexpected (expecting ")")

报错原因：兼容性问题，因为linux将sh默认指向了dash，而不是bash

解决方法：在root下面执行`dpkg-reconfigure dash`,选择no

![img](https://gitee.com/jxprog/PicBed/raw/master/md/2022/01/2022-01-18-212418)