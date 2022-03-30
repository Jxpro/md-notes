# pip 使用相关

## 一、pip换源

常用镜像源如下：

```ini
http://mirrors.aliyun.com/pypi/simple/ 
https://pypi.mirrors.ustc.edu.cn/simple/ 
http://pypi.douban.com/simple/ 
https://pypi.tuna.tsinghua.edu.cn/simple/ 
http://pypi.mirrors.ustc.edu.cn/simple/
```

### 命令行配置

```shell
# 临时使用
pip install pkg -i https://pypi.tuna.tsinghua.edu.cn/simple

# 设为默认
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

### 收到配置文件

-   `linux`下，修改 `~/.pip/pip.conf`，没有则新建一个

-   `windows`下，在`C:\Users\xx\pip`下新建文件`pip.ini`

配置文件内容：

```ini
[global]
index-url=http://mirrors.aliyun.com/pypi/simple/
[install]
trusted-host=mirrors.aliyun.com
```

注意事项：

1.   保存时注意**以UTF-8 无BOM格式编码**
2.   `trusted-host`选项为了避免麻烦是必须的，否则使用的时候会提示不受信任

### 配置pycharm

点击`File >Settings > Project: Worn > Project Interpreter` 右边加号

![image-20220330204103799](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-03-30-204105.png)

然后单击`Manager Repositories`

![image-20220330204047541](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-03-30-204048.png)

最后点击左上角`+`号添加镜像源

![image-20220330204030714](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-03-30-204032.png)

## 二、误删pip解决方法

### 错误一

报错：

`No module named pip.main`

解决：

1.   重新安装pip：`python -m ensurepip`
2.   升级pip：`python -m pip install -U pip`

### 错误二

报错：

`'pip' is a package and cannot be directly executed`

解决：

1.   找到python所在路径：`where/which python`

2.   进入到对应的`site-packages`目录，发现里面有两个pip开头的文件夹：`pip`和`pip-21.3.1.dist-info`

3.   删掉它们，再重新执行错误一的那两个命令

     ```shell
     rm -rf pip*
     python -m ensurepip
     python -m pip install -U pip
     ```

     