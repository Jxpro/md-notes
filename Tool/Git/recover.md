# 找回误删文件

[TOC]

## 一、恢复曾今提交过的记录

可以直接根据`git reflog`返回的结果，用`git reset --hard commit_id`回退到对应这个版本。

>   但是我们如果只是想把此提交给找回来，恢复他，那还是不要用`reset`的方式，可以用`git cherry-pick commitid`单独取一个commit到当前分支或者用`merge`来做合并。

## 二、恢复忘提交或clean的记录

如果你的代码文件没有`commit`过，就被手贱或者`clean`**删除**掉了或者也是被`reset --hard`的时候搞没了。这就**不仅仅**是用一个`git reflog`命令就可以简单找回来的，但只要你以前有做过`add`的操作把他放到过暂存区，我们就可以把他找回来。什么？你连add都没有操作过那就只能**开始准备新一轮的面试**了！！！

**核心命令**：`git fsck --lost-found`,他会通过一些神奇的方式把历史操作过的文件以某种算法算出来加到`.git/lost-found`文件夹里，输出的记录就像下面这个样子：

![img](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2024/01-31-172700.jpg)

>   我们可以看到这里有`blob、commit、tree`类型的数据，还有`tag`等类型的。他们是什么含义呢？
>
>   来，我们都是大神当然要学学**git底层存储方式**，如下图：
>
>   ![img](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2024/01-31-172636.jpg)
>
>   -   `commit`数据结构在每次提交之后都会生成一个，当我们进行`commit`之后，首先会创建一个`commit`组件，之后创建一个`tree`组件，把所有的文件信息都储存在里面,每个`blob`代表一个文件，都可以在`tree`里找到
>   -   `blob`组件并不会对文件信息进行存储，而是只对文件的内容进行记录，文件信息存储在tree里

在上面执行完`git fsck --lost-found`命令后，我们可以：

-   使用`git show`命令可以查看`blob`的内容
-   使用`git cat-file -p`可以看到`commit`的内容，可以选择把这个commit合并到我们的分支里，还是`reset merge rebase cherry-pick`这些命令来合`commit`
-   使用`git ls-tree`列出`tree`下面的文件名和`id`的记录信息，然后就可以根据`blob`的**id**来恢复文件了

>   如果只是为了查看文件，可以这样过滤输出的内容：`git fsck --lost-found | grep 'blob'`

## 三、其他方法

如果你发现执行`git fsck --lost-found`的输出也找不到你想要的，那么只能祭出终极命令来输出近期修改的文件了，如下：

![img](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2024/01-31-172711.jpg)

-   这里用`find .git/objects -type f | xargs ls -lt | sed 3q`这个命令，他的含义是查找`.git/objects`文件夹下的普通文件 按照时间排序后 打印在终端里 sed 3q 是打印3行 sed 100q 是打印100行，随你喜欢
-   `git cat-file -t 7f5965523d2b9e850b39eb46e8e0f7c5755f6719` 就能看见文件类型（`objects/`后面的所有`hex`放在`-t`后面）
-   `git cat-file -p id`就能看见文件内容，是不是很爽，你再用高超的`linux`技巧操作一下，就可以批量打印出全部文件的类型啦

## 四、参考文章

[实习生的代码被弄丢了！救命的时候绝对用的上——每天三分钟玩转Git (8)](https://zhuanlan.zhihu.com/p/66602742)