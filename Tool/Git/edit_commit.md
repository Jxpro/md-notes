# 修改历史commit信息

[TOC]

## 一、修改最新的 log

如果只修改**最新**一条提交信息的`log`内容，直接使用命令 `git commit --amend`  就可以完成修改啦，可以直接参考 git 文档，也就是下面的重写历史功能，比较简单。

## 二、修改历史的 log

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

## 三、修改提交作者或邮箱

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

## 四、rebase 过程报错

**error：The following untracked working tree files would be overwritten by merge**

解决方案：输入`git clean -d -fx`

`clean`具体用法见下一节

## 五、git clean

参考文章：[git clean 的用法详解_一定要学_一定要慎用](https://blog.csdn.net/weixin_44137575/article/details/108142088)

### 5.1 慎用 git clean

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

### 5.2 参数说明

-   n ：显示将要被删除的文件
-   d ：删除未被添加到 git 路径中的文件（将 .gitignore 文件标记的文件全部删除）
-   f ：强制运行
-   x ：删除没有被 track 的文件

### 5.3 运行示例

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

