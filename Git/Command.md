# 一、修改历史commit信息

### （1）修改最新的 log

如果只修改最新一条提交信息的 log  内容,直接使用命令 `git commit --amend`  就可以完成修改啦, 可以直接参考 git 文档,也就是下面的 重写历史 功能,比较简单

## （2）修改历史的 log

如果要修改历史的版本信息(非最新一条),会稍稍麻烦一点,不过也可以搞定,这里用到的方法就是 `git`命令的[重写历史](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E5%86%99%E5%8E%86%E5%8F%B2)功能

1.   执行 git 命令

     ```bash
     # 回溯到近三次的信息
     git rebase -i HEAD~3
     
     # 回溯到根信息
     git rebase -i --root
     ```

2.   找到要修改要的日志,那么修改的日志为，**将`pick` 修改为`edit`, 然后 `:wq` 退出**

3.   执行 `git commit --amend` ，修改完成后 `:wq`  退出

4.   然后执行 `git rebase --continue` ，完成此次 log 的`rebase`

5.   这样本地修改就完成了，可用 `git log` 查看一下

6.   最后 `push` 到远程仓库，加上 `-f` 强制推送：

     ```bash
     git push origin main -f
     ```

     