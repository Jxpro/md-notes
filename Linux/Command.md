## 一、tar & gzip

1.   基础概念
     -   **打包**：将一堆文件或目录什么的变成一个总的文件。
     -   **压缩**：将一个大的文件经过某种压缩算法变成一个小文件。

2.   `tar`命令

     单独使用（`man tar`可以查看 ）

     常用参数 ：

     -   ==-c 打包==
     -   ==-x 解包==
     -   ==-f 指定包的文件名，用在最后一个参数==
     -   -v 详细显示处理的文件
     -   -r 像压缩归档文件末尾追加文件
     -   -u 更新原压缩包中的文件，仅将较新的文件附加到存档中
     -    -t 列出存档中文件的目录

3.   为了方便用户在打包解包的同时压缩或解压文件，tar命令提供了一种特殊的功能，就是可以在打包解包的同时调用其他的压缩程序，比如：gzip，bzip2等。

     1.   tar 调用 gzip：

          gzip是GNU组织开发的一个压缩程序

          .gz结尾的文件就是gzip压缩的结果，与gzip相对的解压程序是gunzip。 

          ==-z 参数令 tar 使用来调用 gzip==

     2.   tar 调用 bzip2

          bzip2是一个压缩能力更强的压缩程序

          .bz2结尾的文件就是bzip2压缩的结果。与bzip2相对的解压程序是bunzip2。

          tar中使用-j这个参数来调用gzip。

4.   `gzip`命令

     缺省情况下， gzip 将原始文件名和时间信息保存在压缩后的文件中。

     gunzip 的功能 与 gzip -d 功能一样。gunzip 将命令行中以.gz, -gz, .z, -z, _z 或 .Z结尾并具有正确标志头的文件解压缩，并以去掉扩展名的解压缩文件替换原文件。

     gunzip 也能识别扩展名为 .tgz 和 .taz 的文件，并将其分别当作 .tar.gz 和  .tar.Z  的缩写。

     gunzip 目前能够解压由 gzip, zip, compress, compress -H 以及 pack产生的文件。 

     对输入格式的检测是自动的。

     ==常用参数：==

     -   -c  将结果写到标准输出，原文件保持不变

     -   -d 解压缩

     -   -r 递归式地查找指定目录并压缩其中的所有文件或者是解压缩

     -   -t 测试，检查压缩文件是否完整

     -   -v 对每一个压缩和解压的文件，显示文件名和压缩比

     -   -l 列出每个压缩文件的如下项目：

         compressed size：压缩文件的长度

         uncompressed size：压缩前文件的长度

         ratio：压缩率（如果未知则为0.0%）

         uncompressed_name：压缩前的文件名
         
         Tips：对于非gzip 格式的文件，压缩前文件长度显示为 -1，例如由compress压缩的 .Z文件。

## 二、systemctl

1.   查询服务是否开机启动

     ```bash
     systemctl is-enabled xxx.service
     ```

2.   开机运行服务 

     ```bash
     systemctl enable xxx.service
     ```

3.   取消开机运行

     ```bash
     systemctl disable xxx.service
     ```

4.   **启动服务**

     ```bash
     systemctl start xxx.service
     ```

5.   **停止服务**

     ```bash
     systemctl stop xxx.service
     ```

6.   **重启服务**

     ```bash
     systemctl restart xxx.service
     ```

7.   重新加载服务配置文件

     ```bash
     systemctl reload xxx.service
     ```

8.   查询服务运行状态

     ```bash
     systemctl status xxx.service
     ```

9.   显示启动失败的服务

     ```bash
     systemctl --failed
     ```

## 三、su 和 sudo

1.   sudo是一种权限管理机制，依赖于/etc/sudoers

     其定义了授权给哪个用户可以以管理员的身份能够执行什么样的管理命令；

     格式：sudo -u username command

     默认情况下，系统只有root用户可以执行sudo命令。需要root用户通过使用visudo命令编辑sudo的配置文件/etc/sudoers，才可以授权其他普通用户执行sudo命令。

2.   su

     su 为`switch user`，即切换用户的简写。

     格式为两种：

     -   su - username （-l为login，即登陆的简写）

     -   su username 

     如果不指定用户名，默认即为root，所以切换到root的身份的命令即为：su [-] [root]

     `su username`与`su - username`的不同之处如下：

     -   su - username 切换用户后，同时切换到新用户的工作环境中。

     -   su username 切换用户后，不改变原用户的工作目录，及其他环境变量目录。
