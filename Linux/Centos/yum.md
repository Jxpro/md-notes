## 一、常用命令

### 1、安装与卸载

|        命令         |    功能    |
| :-----------------: | :--------: |
|   yum install xxx   | 安装安装包 |
|   yum remove xxx    | 卸载安装包 |
| yum groupinsall xxx | 安装程序组 |
| yum groupremove xxx | 卸载程序组 |

### 2、升级

|       命令       |       功能       |
| :--------------: | :--------------: |
| yum check-update | 检查可更新的程序 |
|  yum update xxx  |  更新指定程序包  |
| yum upgrade xxx  |  升级指定程序包  |
|    yum update    |     全部更新     |

### 3、查找

|        命令        |                     功能                     |
| :----------------: | :------------------------------------------: |
|   yum search xxx   |                  查找程序包                  |
|      yum info      |           列出所有已安装程序包信息           |
|    yum info xxx    |                显示程序包信息                |
|      yum list      |      显示所有已经安装和可以安装的程序包      |
|    yum list xxx    |            显示指定程序包安装情况            |
|  yum list updates  |           列出所有可以更新的程序包           |
| yum list installed |            列出所有已安装的程序包            |
|  yum list extras   | 列出所有已安装但不在yum repository中的程序包 |
|  yum deplist xxx   |           查看指定程序包的依赖关系           |
|   yum search xx    |   查找指定程序包，会列出所有包含xxx的包名    |

### 4、缓存

|         命令         |                功能                 |
| :------------------: | :---------------------------------: |
|  yum clean packages  |       清除缓存目录下的软件包        |
|  yum clean headers   |       清除缓存目录下的headers       |
| yum clean oldheaders |      清除缓存目录下旧的headers      |
|   yum clean [all]    | 清除缓存目录下的软件包及旧的headers |
|    yum makecache     |        缓存服务器程序包信息         |

## 二、update和upgrade的区别

>   ~~yum -y update：升级所有包同时，也升级软件和系统内核；~~
>   ~~yum -y upgrade：只升级所有包，不升级软件和系统内核，软件和内核保持原样。~~
>
>   以上说法是经网友测试**可能是错误的**
>
>   
>
>   `yum update`和`yum upgrade`的功能是一样的，都是将package更新至软件源中的最新版
>
>   区别是`yum upgrade`会删除旧版本的package，而`yum update`则会保留
>
>   ~~因此如果软件依赖旧版本的package，最好是使用 yum update，确保不会出现兼容问题~~

个人认为最好`yum update`后 **指定package名称**

**避免升级系统内核的方法**：

直接修改配置文件`/etc/yum.conf`，在 [main] 配置添加配置项

```bash
exclude=kernel*
exclude=centos-release*
```

## 三、切换软件源

1.   CentOS 7的镜像源文件的目录默认都在该路径下：/etc/yum.repos.d/

     ```bash
     cd /etc/yum.repos.d/
     ```

2.   **备份**原来的yum镜像源，如果出现错误可以恢复原配置

     ```bash
     cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
     ```

3.   下载国内镜像源并重命名为 CentOS-Base.repo，将其作为当前系统的镜像源

     ```bash
     # 阿里云镜像站
     wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
     
     # 网易镜像站
     wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo
     ```

4.   清理缓存

     ```bash
     yum clean all
     ```

5.   重新生成缓存

     ```bash
     yum makecache
     ```

     

