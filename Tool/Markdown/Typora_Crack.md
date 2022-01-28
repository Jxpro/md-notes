# 破解 Typora 验证

[TOC]

参考教程：[**Github**](https://github.com/Mas0nShi/typoraCracker/blob/master/README_CN.md)

测试版本：**Typora v 1.0.3**

## 一、下载工具

破解工具：[typora Cracker](https://github.com/Mas0nShi/typoraCracker.git) | [备用地址](https://github.com/Jxpro/MD-Repo/raw/main/typoraCracker-master.zip)

```shell
> python typora.py --help
usage: typora.py [-h] [-u] [-f] asarPath dirPath

[extract and decryption / pack and encryption] app.asar file from [Typora].

positional arguments:
  asarPath    app.asar file path/dir [input/ouput]
  dirPath     as tmp and out directory.

optional arguments:
  -h, --help  show this help message and exit
  -u          pack & encryption (default: extract & decryption)
  -f          enabled prettify/compress (default: disabled)

If you have any questions, please contact [ MasonShi@88.com ]
```

## 二、破解

**解压并解密**`app.asar`文件：

```shell
$ python typora.py {installRoot}/Typora/resources/app.asar workstation/outfile/
...
```

将`workstation/outfile/dec_app/License.js`**替换**为`example/patch/License.js`

重新**加密并打包**文件：

```shell
$ python typora.py -u workstation/outfile/dec_app workstation/outappasar
...
```

生成合格的**许可证**：

```shell
$ node example/keygen.js
LMCHMW-4DGF5Z-T4PP4Z-S6343H
```

使用`workstation/outappasar/app.asar`**替换**`{installRoot}/Typora/resources/app.asar`

然后启动`Typora`输入密钥即可

## 三、注意的坑

### 3.1 路径最好加引号

错误命令：`python typora.py C:/Program Files/Typora/resources/app.asar workstation/outfile/`

错误提示：**typora.py: error: unrecognized arguments: workstation/outfile/**

因为`C:/Program Files/`路径中含有空格，所以需要加上引号：

`python typora.py "C:/Program Files/Typora/resources/app.asar" "workstation/outfile/"`

### 3.2 需手动创建文件夹

执行下面命令时：

```shell
$ python typora.py -u workstation/outfile/dec_app workstation/outappasar
```

报错：

```shell
2021-12-04 14:35:46.711 | ERROR    | __main__:packWenc:103 - plz input Directory for app.asar
Traceback (most recent call last):
  File "typora.py", line 151, in <module>
    main()
  File "typora.py", line 146, in main
    args.mode(args.asarPath, args.dirPath, args.format)
  File "typora.py", line 104, in packWenc
    raise NotADirectoryError
NotADirectoryError
```

原因：`workstation/outappasar`**目录未创建**，需要指定一个已创建的目录或创建指定目录

### 3.3 app.asar的位置

执行下面命令时：

```shell
$ python typora.py {installRoot}/Typora/resources/app.asar workstation/outfile/
```

后面的`workstation/outfile/`可自行指定。但是`app.asar`文件**必须位于**`{installRoot}/Typora/resources/app.asar`下，不然解压后的文件会缺少`main.node`文件，导致打包后`Typora`启动报错！！！

指定安装目录下的app.asar输出如下：

```shell
$ python typora.py "C:/Program Files/Typora/resources/app.asar" "workstation/outfile/"
2021-12-04 14:37:22.030 | INFO     | __main__:extractWdec:56 - extract asar file: C:/Program Files/Typora/resources/app.asar
2021-12-04 14:37:22.093 | SUCCESS  | __main__:extractWdec:59 - extract ended.
2021-12-04 14:37:22.108 | INFO     | __main__:extractWdec:61 - read Directory: workstation/outfile/tmp_app
2021-12-04 14:37:22.124 | INFO     | __main__:extractWdec:66 - set Directory: workstation/outfile\dec_app
...
2021-12-04 14:37:22.530 | INFO     | __main__:extractWdec:73 - open file: FilesOp.js
2021-12-04 14:37:22.546 | SUCCESS  | __main__:extractWdec:81 - decrypt and save file: FilesOp.js
2021-12-04 14:37:22.640 | INFO     | __main__:extractWdec:73 - open file: License.js
2021-12-04 14:37:22.655 | SUCCESS  | __main__:extractWdec:81 - decrypt and save file: License.js
2021-12-04 14:37:22.780 | INFO     | __main__:extractWdec:73 - open file: main.node
2021-12-04 14:37:22.795 | DEBUG    | __main__:extractWdec:78 - skip file: main.node
2021-12-04 14:37:22.812 | SUCCESS  | __main__:extractWdec:81 - decrypt and save file: main.node
2021-12-04 14:37:22.936 | INFO     | __main__:extractWdec:73 - open file: menu.js
2021-12-04 14:37:22.952 | SUCCESS  | __main__:extractWdec:81 - decrypt and save file: menu.js
...
2021-12-04 14:37:23.327 | SUCCESS  | __main__:main:147 - Done!
```

指定当前目录下的app.asar文件输出如下：

```shell
$ python typora.py "./app.asar" "workstation/outfile/local"
2021-12-04 14:37:36.323 | INFO     | __main__:extractWdec:56 - extract asar file: ./app.asar
Couldn't copy file .\main.node, no extracted directory
2021-12-04 14:37:36.369 | SUCCESS  | __main__:extractWdec:59 - extract ended.
2021-12-04 14:37:36.400 | INFO     | __main__:extractWdec:61 - read Directory: workstation/outfile/local\tmp_app
2021-12-04 14:37:36.416 | INFO     | __main__:extractWdec:66 - set Directory: workstation/outfile/local\dec_app
...
2021-12-04 14:37:36.963 | INFO     | __main__:extractWdec:73 - open file: FilesOp.js
2021-12-04 14:37:36.978 | SUCCESS  | __main__:extractWdec:81 - decrypt and save file: FilesOp.js
2021-12-04 14:37:37.088 | INFO     | __main__:extractWdec:73 - open file: License.js
2021-12-04 14:37:37.104 | SUCCESS  | __main__:extractWdec:81 - decrypt and save file: License.js
2021-12-04 14:37:37.197 | INFO     | __main__:extractWdec:73 - open file: menu.js
2021-12-04 14:37:37.213 | SUCCESS  | __main__:extractWdec:81 - decrypt and save file: menu.js
...
2021-12-04 14:37:37.635 | SUCCESS  | __main__:main:147 - Done!
```

>   在第三行其实已经指明了错误：**Couldn't copy file .\main.node, no extracted directory**