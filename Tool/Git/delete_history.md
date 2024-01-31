# 从历史记录中删除文件

[TOC]

当我们不小心通过`git add .`加入了一个**很大的资源文件或者不宜公开的文件**，并`commit`产生了历史记录，还`push`到了远程仓库，这个时候我们想要删除他们，可以借助下面两个命令。

## 一、引言 git rm

-   `git rm file_path`删除暂存区和分支上的文件，同时工作区也不需要，即**删除所有**。
-   `git rm --cached file_path`删除暂存区或分支上的文件，但工作区需要使用，只是不希望被版本控制，适用于已经被`git add`，但是又需要**撤销**的情况。

## 二、关键 git filter-branch

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

## 三、具体步骤

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


## 四、参考文章

[官方文档](https://git-scm.com/docs/git-filter-branch)

[核弹级的git指令 git filter-branch](http://shaoguangleo.github.io/2018/02/01/git-filter-branch/)
