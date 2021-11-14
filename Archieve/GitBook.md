# GitBook从入门到放弃再到成功

## 一、GitBook是什么？

在我认识`GitBook`之前，我已经在使用`Git`了，毋容置疑，`Git`是目前世界上最先进的分布式版本控制系统。我认为`Git`不仅是程序员管理代码的工具，它的分布式协作方式同样适用于很多场合。

乍一看`GitBook`的名字，你可能会认为它是关于`Git`的一本书。而当你有所了解之后，你也许会认为它是一个使用`Git`构建电子书的工具。其实不然，`GitBook`与`Git`的关系，就像`Java`和`JavaScript`那样，没有一点关系！

实际上，`GitBook`是一个基于`Node.js`的命令行工具，支持`Markdown`和`AsciiDoc`两种语法格式，可以输出 HTML、PDF、eBook 等格式的电子书。

## 二、安装Nodejs

`GitBook`是一个基于`Node.js`的命令行工具，故首先需要下载安装 [Node.js](https://npm.taobao.org/mirrors/node/)

~~**注意nodejs版本不能太高，不然可以安装CLI，但安装不了GitBook，这是个大坑！！！**~~

本人在`Windows11`下成功的版本为`v12.22.1`

补充：`Windows10`下，`v16.13.0`也成功，等待时间很长很长

## 三、安装 GitBook

### 3.1输入下面的命令来安装 GitBook-CLI

```bash
npm install gitbook-cli -g
```

### 3.2 移除错误提示

~~找到npm 的全局安装目录，进入到`...\gitbook-cli\node_modules\npm\node_modules`~~

~~执行以下命令更新`graceful-fs`库，不然会提示错误或一直阻塞在`Installing GitBook 3.2.3`~~

```bash
# 将graceful-fs更新到latest版，即最新版
npm install graceful-fs@latest --save
# 以上命令无效
```

找到`/gitbook-cli/node_modules/npm/node_modules/graceful-fs/polyfills.js`文件，

注释以下内容，位于62-64行

```javascript
// fs.stat = statFix(fs.stat)
// fs.fstat = statFix(fs.fstat)
// fs.lstat = statFix(fs.lstat)
```

### 3.3 安装GitBook

执行以下指令查看gitbook版本，程序会自动安装gitbook，且时间会很久

```bash
$ gitbook -V
CLI version: 2.3.2
# 首次运行会提示：
# Installing GitBook 3.2.3
# 而且一般安装很久
GitBook version: 3.2.3
```

## 四、预览

预览命令：`gitbook serve`
预览地址：http://localhost:4000

## 五、构建

构建静态网站：`gitbook build`
创建后，找到_book文件夹下的index.html，双击即可在浏览器预览

## 六、导出PDF

1.   首先安装Calibre插件，下载链接：[calibre release (3.48.0) (calibre-ebook.com)](https://download.calibre-ebook.com/3.48.0/)

     最好安装这个版本，新版本会有问题

2.   将ebook-convert.exe配置到系统变量Path中，否则会失败，该文件在Calibre安装目录下
3.   在所有的一切都准备好之后就可以运行`gitbook pdf`，输出`pdf`了

## 七、参考文章

-   [Gitbook教程（小白入坑gitbook全过程）](https://www.jianshu.com/p/0388d8bb49a7)
-   [GitBook 使用教程](https://www.jianshu.com/p/421cc442f06c)
-   [Gitbook错误"cb.apply is not a function"的解决办法](https://www.jianshu.com/p/6221330b36ba)