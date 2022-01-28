# ROS笔记

[TOC]

## 一、软硬件版本

|         名称         |             版本              |
| :------------------: | :---------------------------: |
| 系统版本（uname -a） | Linux ubuntu 5.4.0-96-generic |
|      rosdistro       |            melodic            |
|      rosversion      |            1.14.12            |

## 二、安装ROS

### 2.1 配置软件仓库

![image-20220128194520014](https://gitee.com/jxprog/PicBed/raw/master/md/2022/01/2022-01-28-194521.png)

### 2.2 添加ROS软件源

```bash
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

### 2.3 添加密钥

```bash
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
```

### 2.4 安装

更新软件索引：

```bash
sudo apt update
```

安装`Desktop-Full`版本（约五到十分钟）：

```bash
sudo apt install ros-melodic-desktop-full
```

### 2.5 设置环境变量

修改`.bashrc`：

```bash
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
```

重启终端或使用`source`命令重新加载：

```bash
source ~/.bashrc
```

### 2.7 安装相关依赖

```bash
sudo apt install python-rosdep python-rosinstall python-rosinstall-generator python-wstool build-essential
```

### 2.8 初始化rosdep

```bash
sudo rosdep init
rosdep update
```

## 三、测试用例

![image-20220128195943951](https://gitee.com/jxprog/PicBed/raw/master/md/2022/01/2022-01-28-195945.png)