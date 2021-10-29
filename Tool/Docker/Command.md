## 常用命令

| 命令                                        | 作用                                             |
| ------------------------------------------- | ------------------------------------------------ |
| docker search xxx                           | **搜索**镜像                                     |
| docker pull xxx                             | **下载**镜像                                     |
| docker start 容器id                         | **启动**容器                                     |
| docker restart 容器id                       | **重启**容器                                     |
| docker stop 容器id                          | **停止**当前正在运行的容器                       |
| docker kill 容器id                          | **强制停止**当前运行的容器                       |
| docker run -d --name tomcat01 tomcat        | 新建容器并以后台运行，并**命名**                 |
| docker run -d -p 主机端口:容器内端口 tomcat | 新建容器并以后台运行，并**映射端口**             |
| docker run -d -v 主机目录:容器内目录 tomcat | 新建容器并以后台运行，并**挂载目录**             |
| docker run -it tomcat /bin/bash             | 以**交互模式新建**容器并使用bash作为伪终端       |
| ~~docker run -it -d tomcat /bin/bash~~      | ~~以交互模式新建容器并 exit 退出后**保持运行**~~ |
| docker exec -it 容器ID/名称 /bin/bash       | 以交互模式**进入**容器并使用bash作为伪终端       |
| docker run -it --rm tomcat /bin/bash        | 交互模式**临时**新建容器                         |
| docker rm -f (docker ps -aq)                | **删除容器**                                     |
| docker rmi -f (docker images -aq)           | **删除镜像**                                     |
| docker inspect 容器ID/名称                  | 查看容器的**元数据**                             |
| docker port 容器ID/名称                     | 查看容器的**映射端口**                           |
| exit                                        | 退出并关闭容器 ~~( -d 可以保持运行 )~~           |
| ctrl +P +Q                                  | 退出但并关闭容器                                 |

## run 命令

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

# 示例一
docker run -d -p 8080:8080 --name tomcat01 tomcat
docker exec -it tomcat01 /bin/bash
exit
docker stop aa664b0c8ed9
```

OPTIONS说明：

-   **-a stdin:** 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；
-   **-d:** 后台运行容器，并返回容器ID；
-   **-i:** 以交互模式运行容器，通常与 -t 同时使用；
-   **-P:** 随机端口映射，容器内部端口**随机**映射到主机的端口
-   **-p:** 指定端口映射，格式为：**主机(宿主)端口:容器端口**
-   **-t:** 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
-   **--name "xxx":** 为容器指定一个名称；
-   **--dns 8.8.8.8:** 指定容器使用的DNS服务器，默认和宿主一致；
-   **--dns-search example.com:** 指定容器DNS搜索域名，默认和宿主一致；
-   **-h :** 指定容器的hostname；
-   **-e username "ritchie":** 设置环境变量；
-   **--env-file:** 从指定文件读入环境变量；
-   **-m :**设置容器使用内存最大值；
-   **--net:** 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；
-   **--link:** 添加链接到另一个容器；
-   **--expose:** 开放一个端口或一组端口；
-   **--volume , -v:** 绑定一个卷

## ps --format

```bash
Valid placeholders:
.ID - Container ID.
.Image - Image ID.
.Command - Quoted command.
.CreatedAt - Time when the container was created.
.RunningFor - Elapsed time since the container was started.
.Ports - Exposed ports.
.Status - Container status.
.Size - Container disk size.
.Names - Container names.
.Labels - All labels assigned to the container.
.Label - Value of a specific label for this container. 
.Mounts - Names of the volumes mounted in this container.

# example
docker ps -a --format "table {{.ID}}\t{{.Command}}\t{{.Names}}\t{{.Status}}"
```

## rm & rmi

```bash
docker rm -f $(docker ps -aq)
docker rmi -f $(docker images -aq)

docker rm 容器id   				#删除指定的容器，强制删除: rm -rf
docker rm -f $(docker ps -aq)  	 #删除所有的容器
docker ps -a -q|xargs docker rm  #删除所有的容器
```

