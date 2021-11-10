# Python程序打包成.exe

>   为了让脚本在他人机器上顺利执行，你首先要帮他安装python解释器，如果你import了一些库比如pandas，那这个也需要安装，甚至相关的依赖也需要安装。这个时间成本显然是很大的，这部分工作也很枯燥，甚至会影响工作积极性，因此花一点时间，学会如何将python脚本打包为.exe文件，无需再安装各种开发环境和依赖库，是一件一劳永逸的事情。
>
>   现在，主流的工具有Pyinstaller、cz_Freeze、py2exe，本文只介绍Pyinstaller的使用。

## 一、安装pyinstaller

```bash
pip install pyinstaller

$ pyinstaller --version
4.6
```

## 二、pyinstaller打包机制

我们写的python脚本是不能脱离python解释器单独运行的，所以在打包的时候，至少会将python解释器和脚本一起打包，同样，为了打包的exe能正常运行，会把我们所有安装的第三方包一并打包到exe。

即使我们的项目只使用的一个requests包，但是可能我们还安装了其他n个包，但是他不管，因为包和包只有依赖关系的。比如我们只装了一个requests包，但是requests包会顺带装了一些其他依赖的小包，所以为了安全，只能将所有第三方包+python解释器一起打包。

## 三、pyinstaller打包exe

### 基本命令：

```bash
# 打包exe
Pyinstaller -F app.py

# 取消控制台输出
Pyinstaller -F -w app.py 

# 指定模块目录
pyinstaller -F -p DIR app.py

# 指定exe图标打包
Pyinstaller -F -i xx.ico app.py 
```

执行上面命令，将看到详细的生成过程。当生成完成后，将会在此 app 目录下看到多了一个 dist 目录，并在该目录下看到有一个 app.exe 文件，这就是使用 PyInstaller 工具生成的 EXE 程序。

## 四、其他常用命令

| -h   | 查看该模块的帮助信息                                         |
| ---- | ------------------------------------------------------------ |
| -F   | 产生单个的可执行文件，覆盖打包                               |
| -D   | 产生一个目录（包含多个文件）作为可执行程序                   |
| -i   | 表示可执行文件的图标                                         |
| -a   | 不包含 Unicode 字符集支持                                    |
| -d   | 产生 debug 版本的可执行文件                                  |
| -w   | 指定程序运行时不显示命令行窗口（仅对 Windows 有效）          |
| -c   | 指定使用命令行窗口运行程序（仅对 Windows 有效）（默认）      |
| -o   | 指定 spec 文件的生成目录。如果没有指定，则默认使用当前目录来生成 spec 文件 |
| -p   | 设置 Python 导入模块的路径，可使用路径分隔符（Windows 使用分号，Linux 使用冒号）来分隔多个路径 |
| -n   | 指定项目（产生的 spec）名字，如果省略该选项，那么第一个脚本的主文件名将作为 spec 的名字 |

## 五、问题

问题描述：python打包exe后**缺少模块**，报错`ModuleNotFoundError`

解决办法：

1.   Pyinstaller会从本地的环境路径下找需要的模块，例如python的包都放在site-packages目录下，因此，Pyinstalled可以直接从该目录下获取需要的模块。如果本地也缺少该模块，先通过pip命令安装。例如：`pip install requests`

2.   如果你需要的模块不在site-packages，而在你指定的目录下，那打包时你可以通过-p DIR参数指定，其中DIR为你需要的模块路径：`pyinstaller -F -w -p DIR app.py`

3.   有时候并不是找不到模块，而是代码中有些模块是隐含导入的，这样的话就需要指出这些模块，才能正确的打包，可以在命令行打包时使用参数`--hidden-import MODULENAME`指定模块名，或者可以在打包后生成的`app.spec`文件中修改，该文件中有个参数`hiddenimports=[]`，配置为：`hiddenimports=['cython','sklearn','sklearn.ensemble',...]`，然后再运行以下命令 `pyinstaller app.spec`