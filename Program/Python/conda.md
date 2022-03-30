# Conda使用相关

## 环境变量

可选配置，或者在安装执行执行`conda init`也可

```ini
D:\Anaconda3\Scripts
D:\Miniconda3\condabin
D:\Anaconda3\Library\bin
```

## 环境管理

查看环境管理的全部命令帮助

```shell
conda env -h
```

![image-20220331002229281](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-03-31-002230.png)

创建环境

```shell
conda create -n env_name
```

输入`y`确认创建。

创建制定python版本的环境

```shell
conda create -n env_name python=2.7
conda create -n env_name python=3
conda create -n env_name python=3.5
```

创建包含某些包的环境

```shell
conda create --name env_name numpy scipy1
```

创建指定python版本下包含某些包的环境

```shell
conda create --name your_env_name python=3.5 numpy scipy1
```

列举环境

```shell
conda info --envs
conda env list
```

进入环境

```shell
conda activate env_name
```

退出环境

```shell
codna deactivate
```

复制环境

```shell
conda create -n new_env_name --clone old_env_name
```

删除环境

```shell
conda remove -n env_name --all
```

## 重置环境

```shell 
# 查看环境记录
conda list --revisions

# 回到指定版本
conda install --rev 0
```

## 重命名环境

Conda是没有重命名环境的功能的, 要实现这个基本需求, 只能通过愚蠢的**克隆-删除**的过程

切记不要直接`mv`移动环境的文件夹来重命名, 会导致一系列无法想象的错误的发生

```shell
//克隆oldname环境为newname环境
conda create -n new_env_name --clone old_env_name 
//彻底删除旧环境
conda remove -n old_env_name --all  
```

>   最好在base环境下进行以上操作，否则会出现各种莫名的问题

## 备份环境

首先通过`conda activate target_env`要备份的环境`target_env`，然后输入下面的命令会在当前工作目录下生成一个`environment.yml`文件

```shell
conda env export > environment.yml
```

拿到`environment.yml`文件后，将该文件放在工作目录下，可以通过以下命令即可从该文件创建环境

```shell
conda env create -f environment.yml
```

## 包管理

列举当前活跃环境下的所有包

```shell
conda list
```

列举一个非当前活跃环境下的所有包

```shell
conda list -n env_name
```

为指定环境安装某个包

```shell
conda install -n env_name package_name
```

查看指定包可安装版本

```shell
conda search package_name
```

 安装本地包

>   有时conda或pip源下载速度太慢，`install`过程中会中断连接导致压缩包下载不全，此时，我们可以用浏览器等工具先下载指定包再用conda或pip进行本地安装

```bash
#pip 安装本地包
pip install   ～/Downloads/a.whl
#conda 安装本地包
conda install --use-local  ~/Downloads/a.tar.bz2
```

## 清理瘦身

```shell
conda clean -p       //删除没有用的包
conda clean -t       //删除tar包
conda clean -y -a    //删除所有的安装包及cache
```

## 关闭/自动激活base

```bash
conda activate   #默认激活base环境
conda activate xxx  #激活xxx环境
conda deactivate #关闭当前环境
conda config --set auto_activate_base false  #关闭自动激活状态
conda config --set auto_activate_base true  #关闭自动激活状态
12345
```

## 为所有shell初始化conda

`conda`在`Windows`上的默认`shell`是`cmd`，而不是`PowerShell`，所以在`PowerShell`中使用一般的`conda`命令是可以 ， 但无法使用`activate`、`deactivate`切换环境 

通过`conda init`命令，可以为让所有`shell`都使用全部的`conda`命令

```shell
# 初始化 conda
conda init [shell_name]

# 恢复初始化
conda init --reverse
```

![image-20220331004043480](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-03-31-004950.png)