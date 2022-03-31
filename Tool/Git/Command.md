# Git的基本使用

[TOC]

## 一、常用命令

| 命令                              | 作用                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| git status                        | 查看文件树**状态**                                           |
| git clone < url >                 | **克隆**仓库                                                 |
| git add < file > ...              | **暂存**工作区文件                                           |
| git commit -m "xxx"               | 以"xxx"消息**提交修改**                                      |
| git push -f origin main           | **强制推送**到远程仓库                                       |
| git restore < file > ...          | **撤销工作区**修改                                           |
| git restore --staged < file > ... | **撤销暂存区**修改                                           |
| git branch [-a] [-v]              | 显示**分支列表** [-a显示**远程**分支] [-v显示最后一次**提交信息**] |
| git checkout [-b] branch          | [**创建**并] **切换**到 branch **分支**                      |
| git switch [-c] branch            | [创建并] 切换到 branch 分支（用于**替代checkout**）          |
| git remote [-v]                   | 显示**远程仓库列表** [-v显示 **url** ]                       |
| git remote show origin            | 显示远程仓库origin**详细信息**                               |
| git pull[^1] origin main[:repo]   | **拉取**远程仓库分支到本地并 [与repo ] **合并**              |
| git fetch origin main[:repo]      | **拉取**远程仓库分支到本地 [并命名为 repo ]                  |
| git merge main                    | 将main分支与当前分支**合并**                                 |

[^1]:pull 根据不同的配置，可等于 fetch + merge 或 fetch + rebase（详细见下文）

## 二、pull与fetch区别

`pull`根据不同的配置，可等于`fetch + merge`或`fetch + rebase`：

-   参考文章：[git pull 和 git fetch的区别](https://www.zhihu.com/question/38305012)
-   图片说明：
![image-20211101144612683](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2021/11/01-144615.png)

## 三、修改历史commit信息

### 3.1 修改最新的 log

如果只修改**最新**一条提交信息的 log  内容,直接使用命令 `git commit --amend`  就可以完成修改啦, 可以直接参考 git 文档,也就是下面的 重写历史 功能,比较简单

### 3.2 修改历史的 log

如果要修改**历史**的版本信息(非最新一条)，这里用到的方法就是 `git`命令的[重写历史](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E5%86%99%E5%8E%86%E5%8F%B2)功能

1.   执行`git`命令
     ```shell
     # 回溯到近三次的信息
     git rebase -i HEAD~3
     
     # 回溯到根信息
     git rebase -i --root
     ```
2.   找到要修改要的`log`，将其`pick` 修改为`edit`, 然后 `:wq` 退出
3.   然后修改对应的提交消息，修改完成后 `:wq`  退出
4.   执行 `git commit --amend`重新提交
5.   然后执行 `git rebase --continue` ，完成此次`log`的`rebase`
6.   这样**本地修改**就完成了，可用 `git log` 查看一下，且发现`commit`的时间没有变化
7.   最后 `push` 到远程仓库，加上 `-f` **强制推送**
     ```shell
     git push -f origin main
     ```
8.   但是在`GitHub`上查看会发现`commit`的时间发生了变化
9.   为此可执行以下指令来恢复时间戳
     ```shell
     git rebase -i --root --committer-date-is-author-date
     ```
     -   必须加上`-i --root`或`-i HEAD~3`，不然`--committer-date-is-author-date`会重置`rebase`的更改
     -   `-i --root`弹出的窗口直接关闭，不用选择任何一项`edit`，然后等待`rebase`完成即可
10.   最后再次`push` 到远程仓库，加上 `-f` **强制推送**
      ```shell
      git push -f origin main
      ```

### 3.3 rebase 过程报错

**error：The following untracked working tree files would be overwritten by merge**

解决方案：输入`git clean -d -fx`

`clean`具体用法见下一节

## 四、git clean 的用法详解

参考文章：[git clean 的用法详解_一定要学_一定要慎用](https://blog.csdn.net/weixin_44137575/article/details/108142088)

### 4.1 慎用 git clean

git clean 从你的工作目录中删除所有**没有 tracked ，没有被管理**过的文件。删除了找回很麻烦，甚至很难找回来了，所以一定要**慎用**!!!

>   **git clean 和 git reset --hard 结合使用。**
>
>   clean 影响没有被 track 过的文件（清除未被 add 或被 commit 的本地修改）
>
>   reset 影响被 track 过的文件 （回退到上一个 commit）
>
>   所以需要 clean 来删除没有 track 过的文件，reset 删除被 track 过的文件
>
>   结合两命令 → 让你的工作目录**完全**回到一个指定的 <commit> 的状态

### 4.2 参数说明

-   n ：显示将要被删除的文件
-   d ：删除未被添加到 git 路径中的文件（将 .gitignore 文件标记的文件全部删除）
-   f ：强制运行
-   x ：删除没有被 track 的文件

### 4.3 运行示例

```shell
git clean -n
// 是一次 clean 的演习, 告诉你哪些文件会被删除，不会真的删除

git clean -f
// 删除当前目录下所有没有 track 过的文件
// 不会删除 .gitignore 文件里面指定的文件夹和文件, 不管这些文件有没有被 track 过

git clean -f <path>
// 删除指定路径下的没有被 track 过的文件

git clean -df
// 删除当前目录下没有被 track 过的文件和文件夹

git clean -xf
// 删除当前目录下所有没有 track 过的文件.
// 不管是否是 .gitignore 文件里面指定的文件夹和文件

git clean
// 对于刚编译过的项目也非常有用
// 如, 他能轻易删除掉编译后生成的 .o 和 .exe 等文件. 这个在打包要发布一个 release 的时候非常有用

git reset --hard
git clean -df
git status
// 运行后, 工作目录和缓存区回到最近一次 commit 时候一摸一样的状态。
// 此时建议运行 git status，会告诉你这是一个干净的工作目录, 又是一个新的开始了！
```

## 五、找回误删文件

参考文章：[实习生的代码被弄丢了！救命的时候绝对用的上——每天三分钟玩转Git (8)](https://zhuanlan.zhihu.com/p/66602742)

### 5.1 恢复曾今提交过的记录

可以直接根据`git reflog`返回的结果，用`git reset --hard commit_id`回退到对应这个版本

>   但是我们如果只是想把此提交给找回来，恢复他，那还是不要用`reset`的方式，可以用`git cherry-pick commitid`单独取一个commit到当前分支或者用`merge`来做合并。

### 5.2 恢复忘提交或clean的记录

如果你的代码文件没有`commit`过，就被手贱或者`clean`**删除**掉了或者也是被`reset --hard`的时候搞没了。这就**不仅仅**是用一个`git reflog`命令就可以简单找回来的，但只要你以前有做过`add`的操作把他放到过暂存区，我们就可以把他找回来。什么？你连add都没有操作过那就只能**开始准备新一轮的面试**了！！！

**核心命令**：`git fsck --lost-found`,他会通过一些神奇的方式把历史操作过的文件以某种算法算出来加到`.git/lost-found`文件夹里，输出的记录就像下面这个样子。

![img](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2021/11/2021-11-30-132836.jpg)

>   我们可以看到这里有`blob、commit、tree`类型的数据，还有`tag`等类型的。他们是什么含义呢？
>
>   来，我们都是大神当然要学学**git底层存储方式**，如下图：
>
>   ![img](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2021/11/2021-11-30-133209.jpg)
>
>   -   `commit`数据结构在每次提交之后都会生成一个，当我们进行`commit`之后，首先会创建一个`commit`组件，之后创建一个`tree`组件，把所有的文件信息都储存在里面,每个`blob`代表一个文件，都可以在`tree`里找到
>   -   `blob`组件并不会对文件信息进行存储，而是只对文件的内容进行记录，文件信息存储在tree里

在上面执行完`git fsck --lost-found`命令后，我们可以：

-   使用`git show`命令可以查看`blob`的内容
-   使用`git cat-file -p`可以看到`commit`的内容，可以选择把这个commit合并到我们的分支里，还是`reset merge rebase cherry-pick`这些命令来合`commit`
-   使用`git ls-tree`列出`tree`下面的文件名和`id`的记录信息，然后就可以根据`blob`的**id**来恢复文件了

>   如果只是为了查看文件，可以这样过滤输出的内容：`git fsck --lost-found | grep 'blob'`

### 5.3 其他方法

如果你发现执行`git fsck --lost-found`的输出也找不到你想要的，那么只能祭出终极命令来输出近期修改的文件了，如下：

![img](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2021/11/2021-11-30-133627.jpg)

-   这里用`find .git/objects -type f | xargs ls -lt | sed 3q`这个命令，他的含义是查找`.git/objects`文件夹下的普通文件 按照时间排序后 打印在终端里 sed 3q 是打印3行 sed 100q 是打印100行，随你喜欢。
-   `git cat-file -t 7f5965523d2b9e850b39eb46e8e0f7c5755f6719` 就能看见文件类型（`objects/`后面的所有`hex`放在`-t`后面）
-   `git cat-file -p id`就能看见文件内容，是不是很爽，你再用高超的`linux`技巧操作一下，就可以批量打印出全部文件的类型啦
