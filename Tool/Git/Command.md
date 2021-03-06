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

`pull`根据不同的配置，可等于`fetch + merge`或`fetch + rebase`。

-   参考文章：[git pull 和 git fetch的区别](https://www.zhihu.com/question/38305012)
-   图片说明：
![image-20211101144612683](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2021/11/01-144615.png)

## 三、修改历史commit信息

### 3.1 修改最新的 log

如果只修改**最新**一条提交信息的`log`内容，直接使用命令 `git commit --amend`  就可以完成修改啦，可以直接参考 git 文档，也就是下面的重写历史功能，比较简单。

### 3.2 修改历史的 log

如果要修改**历史**的版本信息(非最新一条)，这里用到的方法就是 `git`命令的[重写历史](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E5%86%99%E5%8E%86%E5%8F%B2)功能：

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

### 3.3 修改提交作者或邮箱

由于电脑配置`git`的账号填错了邮箱或用户名，导致`github`的提交记录提交错误，为了将之前的提交用户信息都恢复至正确的账号上，所以需要修改本地的git用户信息：

1.   首先修改`user.name`和`user.email`

     ```shell
     $ git config user.name 'xxx'
     $ git config user.email xxx@xx.com
     ```

2.   查询之前的提交记录

     ```shell
     # 查询到近三次的信息
     git rebase -i HEAD~3
     
     # 查询到根信息
     git rebase -i --root
     ```

3.   找到要修改要的日志，将其`pick` 修改为`edit`, 然后 `:wq` 退出

4.   执行 `git commit --amend --reset-author`重新提交并重置作者

5.   然后执行 `git rebase --continue` ，完成此次`log`的`rebase`

6.   但此时提交的时间戳也被改变，使用`--committer-date-is-author-date`也会将时间戳变为`reset-author`后提交的时间，**即使是reset的用户名和之前的用户名一致**

7.   为此，我们需要在`Step 4`使用`git commit --amend`时加上`--date="commit_time"`来指定时间戳，或者在`reset-author`以后再进行一次`rebase`来修改时间戳，其基本格式如下

     ```shell
     --date="Thu, 31 Mar 2022 00:54:24 +0800"
     ```

8.   同样，要使``GitHub`上的时间戳也发生改变，需要在`--date`后再次使用`--committer-date-is-author-date`

9.   最后再次`push` 到远程仓库，加上 `-f` **强制推送**

     ```shell
     git push -f origin main
     ```

### 3.4 rebase 过程报错

**error：The following untracked working tree files would be overwritten by merge**

解决方案：输入`git clean -d -fx`

`clean`具体用法见下一节

## 四、git clean 的用法详解

参考文章：[git clean 的用法详解_一定要学_一定要慎用](https://blog.csdn.net/weixin_44137575/article/details/108142088)

### 4.1 慎用 git clean

git clean 从你的工作目录中删除所有**没有 tracked ，没有被管理**过的文件。删除了找回很麻烦，甚至很难找回来了，所以一定要**慎用**!!!

>   **git clean 和 git reset --hard 结合使用**
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

可以直接根据`git reflog`返回的结果，用`git reset --hard commit_id`回退到对应这个版本。

>   但是我们如果只是想把此提交给找回来，恢复他，那还是不要用`reset`的方式，可以用`git cherry-pick commitid`单独取一个commit到当前分支或者用`merge`来做合并。

### 5.2 恢复忘提交或clean的记录

如果你的代码文件没有`commit`过，就被手贱或者`clean`**删除**掉了或者也是被`reset --hard`的时候搞没了。这就**不仅仅**是用一个`git reflog`命令就可以简单找回来的，但只要你以前有做过`add`的操作把他放到过暂存区，我们就可以把他找回来。什么？你连add都没有操作过那就只能**开始准备新一轮的面试**了！！！

**核心命令**：`git fsck --lost-found`,他会通过一些神奇的方式把历史操作过的文件以某种算法算出来加到`.git/lost-found`文件夹里，输出的记录就像下面这个样子：

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

-   这里用`find .git/objects -type f | xargs ls -lt | sed 3q`这个命令，他的含义是查找`.git/objects`文件夹下的普通文件 按照时间排序后 打印在终端里 sed 3q 是打印3行 sed 100q 是打印100行，随你喜欢
-   `git cat-file -t 7f5965523d2b9e850b39eb46e8e0f7c5755f6719` 就能看见文件类型（`objects/`后面的所有`hex`放在`-t`后面）
-   `git cat-file -p id`就能看见文件内容，是不是很爽，你再用高超的`linux`技巧操作一下，就可以批量打印出全部文件的类型啦

## 六、子模块

参考文章：

-   [Git 工具 - 子模块(submodule)：一个仓库包含另一个仓库](https://www.tsingfun.com/html/2020/opensource_0919/git-submodule.html)
-   [Git子模块：一个仓库包含另一个仓库](https://www.bloghome.com.cn/post/git-zi-mo-kuai-yi-ge-cang-ku-bao-han-ling-yi-ge-cang-ku.html)

### 6.1 问题

有种情况我们经常会遇到：某个工作中的项目需要包含并使用另一个项目。 也许是第三方库，或者你独立开发的，用于多个父项目的库。 现在问题来了：你想要把它们当做两个独立的项目，同时又想在一个项目中使用另一个。

### 6.2 解决方案

子模块（`git submodule`）允许你将一个 Git 仓库作为另一个 Git 仓库的子目录。 它能让你将另一个仓库克隆到自己的项目中，同时还保持提交的独立。

### 6.3 现有仓库中加入子模块

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

### 6.4 克隆包含子模块的仓库

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

### 6.5 GitHub实践

对于托管在`GitHub`上**包含子模块**的项目：

-   如子模块项目托管在`GitHub`上，`GitHub`会为其生成一个链接，表现为字体变蓝，点击可跳转
-   如子模块项目托管在如`Gitee`等其他平台上，`GitHub`无法生成跳转链接，字体保持灰色

![image-20220408001608682](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-04-08-001610.png)

## 七、从历史记录中删除文件

当我们不小心通过`git add .`加入了一个**很大的资源文件或者不宜公开的文件**，并`commit`产生了历史记录，还`push`到了远程仓库，这个时候我们想要删除他们，可以借助下面两个命令。

参考文章：

[官方文档](https://git-scm.com/docs/git-filter-branch)

[核弹级的git指令 git filter-branch](http://shaoguangleo.github.io/2018/02/01/git-filter-branch/)

### 7.1 引言 git rm

-   `git rm file_path`删除暂存区和分支上的文件，同时工作区也不需要，即**删除所有**。
-   `git rm --cached file_path`删除暂存区或分支上的文件，但工作区需要使用，只是不希望被版本控制，适用于已经被`git add`，但是又需要**撤销**的情况。

### 7.2 关键 git filter-branch

如果想要**通过脚本的方式**改写大量提交的话，例如全局修改你的邮箱地址或从每一个提交中移除一个文件，就可以使用 `filter-branch`，它能**改写历史中大量的提交**，除非你的项目还没有公开并且其他人没有基于要改写的工作的提交做的工作，否则你不应当使用它，但是有时它非常的有用。

```
git filter-branch [--setup <command>] [--subdirectory-filter <directory>]
	[--env-filter <命令>] [--tree-filter <命令>]
	[--index-filter <command>] [--parent-filter <command>]
	[--msg-filter <命令>] [--commit-filter <命令>]
	[--tag-name-filter <command>] [--prune-empty]
	[--original <命名空间>] [-d <目录>] [-f |  - 力量]
	[--state-branch <branch>] [--] [<rev-list options>…]
```

### 7.3 具体步骤

1.   使用`git filter-branch`重写历史记录，使用`git rm --cached`将其**从缓存中删除**，且由于它何时加入历史对我们来说并不重要，所以我们还需要添加`--ignore-unmatch`

     ```shell
     # 其中 --force 表示强制执行
     # --index-filter 为固定格式，会在每次历史中执行后面的命令
     # --prune-empty 用于防止一些过滤器会生成空提交，此选项会删除此类提交
     # --tag-name-filter cat 用于更新标签
     # -- --all 重写全部，也可以使用 HEAD~8..HEAD 形式，指定具体范围
     git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch target' --prune-empty --tag-name-filter cat -- --all
     ```

     

2.   删除`git`历史文件备份，参考文章：[git底层原理](https://baijiahao.baidu.com/s?id=1687478489959361341)

     ```shell
     # 二选一即可，或者全部执行
     rm -rf .git/refs/original/
     git for-each-ref --format='delete %(refname)' refs/original | git update-ref --stdin
     ```

3.   设置`reflog`的过期时间为现在，这样放弃了所有历史的找回功能，参考文章：[git-reflog的用法总结](https://blog.csdn.net/chaiyu2002/article/details/81773041)

     ```shell
     git reflog expire --expire=now --all
     ```

4.   清理不必要的文件并优化本地存储库

     ```shell
     git gc --aggressive --prune=now
     ```

5.   最后强制推送到远程仓库

     ```shell
     git push origin --force --all
     ```

     
