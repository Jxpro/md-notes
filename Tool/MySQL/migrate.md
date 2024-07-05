# 迁移 MySQL 数据库

[TOC]

在现代软件开发和维护过程中，数据库迁移是一项常见且关键的任务，尤其是在升级硬件、改善性能或是扩展业务时，经常需要将数据从一个 `MySQL` 迁移到另一个 `MySQL` 上，下面将详细介绍如何实现数据库的迁移。

## 一、备份源数据库

首先，我们需要从源数据库服务器上备份需要迁移的数据库。这里推荐使用 `mysqldump` 命令，这是 `MySQL` 自带的一个非常强大的数据库导出工具。使用该命令可以将数据库的所有数据导出到一个 `SQL` 文件中，这个文件后续可以用来恢复到任何 `MySQL` 服务器上。

假设你的数据库名称为 `source_db`，MySQL的用户名为 `root`，密码为 `password`，

```shell
mysqldump -u root -p source_db > source_db_backup.sql
```

正确输入密码后，`mysqldump` 会将 `source_db` 数据库的所有内容导出到 `source_db_backup.sql` 文件中。

## 二、创建目标数据库

在目标 `MySQL` 服务器上，我们需要创建一个新的数据库，用来接收从源数据库中导出的数据。

假设新数据库命名为 `target_db`，它可以并且建议与 `source_db` 同名，执行下面命令：

```sql
CREATE DATABASE target_db;
```

## 三、恢复数据到目标数据库

有了备份文件和新的数据库后，接下来我们将备份文件中的数据导入到新创建的数据库中，执行以下命令：

```shell
mysql -u root -p target_db < source_db_backup.sql
```

正确输入密码后，命令会开始执行，`source_db_backup.sql` 文件中的数据将被导入到 `target_db` 数据库中。

## 四、验证数据迁移

数据迁移完成后，注意验证数据是否完整无误地导入了新的数据库。

可以通过查询一些核心数据表的记录数，或者执行一些关键的业务查询来检查数据的完整性和准确性。