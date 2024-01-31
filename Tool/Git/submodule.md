# Git 子模块

[TOC]

## 一、问题

有种情况我们经常会遇到：某个工作中的项目需要包含并使用另一个项目。 也许是第三方库，或者你独立开发的，用于多个父项目的库。 现在问题来了：你想要把它们当做两个独立的项目，同时又想在一个项目中使用另一个。

## 二、解决方案

子模块（`git submodule`）允许你将一个 Git 仓库作为另一个 Git 仓库的子目录。 它能让你将另一个仓库克隆到自己的项目中，同时还保持提交的独立。

## 三、现有仓库中加入子模块

1.   以下命令可在现有仓库中**添加子模块**

     ```shell
     git submodule add https://github.com/username/repo
     ```

2.   命令执行后，仓库根目录下增加了一个文件和一个目录：`.gitmodules`和`repo`文件夹

3.   `.gitmodules`文件内容如下：

     ```ini
     [submodule "repo"]
         path = repo
         url = https://github.com/username/repo
     ```

## 四、克隆包含子模块的仓库

-   方法一：克隆项目，并且初始化子模块，更新子模块代码

    ```shell
    # 1.克隆项目，子模块目录默认被克隆，但是是空的
    git clone https://github.com/username/repo_contain_sub
    cd repo_contain_sub
    # 2.初始化子模块：初始化本地配置文件
    git submodule init
    # 3.该项目中抓取所有数据并检出父项目中列出的合适的提交
    git submodule update
    ```

-   方法二：用`--recursive`命令，跟方法一达到同样效果

    ```shell
    git clone https://github.com/username/repo_contain_sub --recursive
    ```

## 五、GitHub实践

对于托管在`GitHub`上**包含子模块**的项目：

-   如子模块项目托管在`GitHub`上，`GitHub`会为其生成一个链接，表现为字体变蓝，点击可跳转
-   如子模块项目托管在如`Gitee`等其他平台上，`GitHub`无法生成跳转链接，字体保持灰色

![image-20220408001608682](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2024/01-31-171809.png)

## 六、参考文章

-   [Git 工具 - 子模块(submodule)：一个仓库包含另一个仓库](https://www.tsingfun.com/html/2020/opensource_0919/git-submodule.html)
-   [Git子模块：一个仓库包含另一个仓库](https://www.bloghome.com.cn/post/git-zi-mo-kuai-yi-ge-cang-ku-bao-han-ling-yi-ge-cang-ku.html)
