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
| git config --global (--unset) ... | （取消）设置**配置项**（core.editor "code --wait"/vim）      |

[^1]:pull 根据不同的配置，可等于 fetch + merge 或 fetch + rebase（详细见下文）

## 二、pull & fetch

`pull`根据不同的配置，可等于`fetch + merge`或`fetch + rebase`。

-   参考文章：[git pull 和 git fetch的区别](https://www.zhihu.com/question/38305012)
-   图片说明：
![image-20211101144612683](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2021/11/01-144615.png)

