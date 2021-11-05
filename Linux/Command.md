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

## 四、sed命令

>   `sed`是一种流编辑器，它是文本处理中非常中的工具，能够完美的配合正则表达式使用，功能不同凡响。处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space），接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有 改变，除非你使用重定向存储输出。Sed主要用来自动编辑一个或多个文件；简化对文件的反复操作；编写转换程序等。

### sed命令格式

命令格式

```bash
sed [options] 'command' file(s)
sed [options] -f scriptfile file(s)
```

-   选项 

```bash
-e<script>或--expression=<script>：以选项中的指定的script来处理输入的文本文件；
-f<script文件>或--file=<script文件>：以选项中指定的script文件来处理输入的文本文件；
-h或--help：显示帮助；
-n或--quiet或——silent：仅显示script处理后的结果；
-V或--version：显示版本信息。
```

-   参数 

    文件：指定待处理的文本文件列表。

### sed命令 

```bash
a\ 在当前行下面插入文本。
i\ 在当前行上面插入文本。
c\ 把选定的行改为新的文本。
d 删除，删除选择的行。
D 删除模板块的第一行。
s 替换指定字符
h 拷贝模板块的内容到内存中的缓冲区。
H 追加模板块的内容到内存中的缓冲区。
g 获得内存缓冲区的内容，并替代当前模板块中的文本。
G 获得内存缓冲区的内容，并追加到当前模板块文本的后面。
l 列表不能打印字符的清单。
n 读取下一个输入行，用下一个命令处理新的行而不是用第一个命令。
N 追加下一个输入行到模板块后面并在二者间嵌入一个新行，改变当前行号码。
p 打印模板块的行。
P(大写) 打印模板块的第一行。
q 退出Sed。
b lable 分支到脚本中带有标记的地方，如果分支不存在则分支到脚本的末尾。
r file 从file中读行。
t label if分支，从最后一行开始，条件一旦满足或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。
T label 错误分支，从最后一行开始，一旦发生错误或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。
w file 写并追加模板块到file末尾。  
W file 写并追加模板块的第一行到file末尾。  
! 表示后面的命令对所有没有被选定的行发生作用。  
= 打印当前行号码。  
# 把注释扩展到下一个换行符以前。
```

### sed替换标记 

```bash
g 表示行内全面替换。  
p 表示打印行。  
w 表示把行写入一个文件。  
x 表示互换模板块中的文本和缓冲区中的文本。  
y 表示把一个字符翻译为另外的字符（但是不用于正则表达式）
\1 子串匹配标记
& 已匹配字符串标记
```

### sed元字符集 

```bash
^ 匹配行开始，如：/^sed/匹配所有以sed开头的行。
$ 匹配行结束，如：/sed$/匹配所有以sed结尾的行。
. 匹配一个非换行符的任意字符，如：/s.d/匹配s后接一个任意字符，最后是d。
* 匹配0个或多个字符，如：/*sed/匹配所有模板是一个或多个空格后紧跟sed的行。
[] 匹配一个指定范围内的字符，如/[ss]ed/匹配sed和Sed。  
[^] 匹配一个不在指定范围内的字符，如：/[^A-RT-Z]ed/匹配不包含A-R和T-Z的一个字母开头，紧跟ed的行。
\(..\) 匹配子串，保存匹配的字符，如s/\(love\)able/\1rs，loveable被替换成lovers。
& 保存搜索字符用来替换其他字符，如s/love/**&**/，love这成**love**。
\< 匹配单词的开始，如:/\<love/匹配包含以love开头的单词的行。
\> 匹配单词的结束，如/love\>/匹配包含以love结尾的单词的行。
x\{m\} 重复字符x，m次，如：/0\{5\}/匹配包含5个0的行。
x\{m,\} 重复字符x，至少m次，如：/0\{5,\}/匹配至少有5个0的行。
x\{m,n\} 重复字符x，至少m次，不多于n次，如：/0\{5,10\}/匹配5~10个0的行。
```

### sed用法实例 

-   [Linux之sed命令详解](https://www.linuxprobe.com/linux-sed-command.html)

-   [sed命令](https://man.linuxde.net/sed)
