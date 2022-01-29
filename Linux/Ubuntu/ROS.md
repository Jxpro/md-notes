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

```shell
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

### 2.3 添加密钥

```shell
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
```

### 2.4 安装

更新软件索引：

```shell
sudo apt update
```

安装`Desktop-Full`版本（约五到十分钟）：

```shell
sudo apt install ros-melodic-desktop-full
```

### 2.5 设置环境变量

修改`.bashrc`：

```shell
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
```

重启终端或使用`source`命令重新加载：

```shell
source ~/.bashrc
```

### 2.7 安装相关依赖

```shell
sudo apt install python-rosdep python-rosinstall python-rosinstall-generator python-wstool build-essential
```

### 2.8 初始化rosdep

```shell
sudo rosdep init
rosdep update
```

## 三、运行海龟测试用例

![image-20220128195943951](https://gitee.com/jxprog/PicBed/raw/master/md/2022/01/2022-01-28-195945.png)

启动以后：

1.   可通过方向键控制海龟移动
2.   如下通过topic消息移动

![image-20220128202259525](https://gitee.com/jxprog/PicBed/raw/master/md/2022/01/2022-01-28-202300.png)

## 四、创建工作空间与功能包

### 4.1 创建并初始化工作区

```shell
mkdir -p ~/catkin_ws/src
cd catkin_ ws/src
catkin_init_workspace
```

### 4.2 编译工作空间（可为空）

```shell
cd catkin_ws/
catkin_make         #不产生install文件夹
catkin_make install #产生install文件夹
```

### 4.3 设置环境变量

```shell
source devel/setup.bash

echo $ROS_PACKAGE_PATH
```

### 4.4 创建功能包（开发的最小模块单位）

```shell
cd ~/catkin_ws/src
catkin_create_pkg test_pkg std_msgs rospy roscpp
```

### 4.5 编译功能包

```shell
cd ~/catkin_ws
catkin_make
source ~/catkin_ws/devel/setup.bash
```

>   同一个工作空间下,不允许存在同名功能包；但不同工作空间下,允许存在同名功能包

## 五、程序控制海龟示例

创建功能包：

```shell
$ catkin_create_pkg learning_topic roscpp rospy std_msgs geometry_msgs turtlesim
```

### C++实现

代码：

```c++
/**
 * 该例程将发布turtle1/cmd_vel话题，消息类型geometry_msgs::Twist
 */
 
#include <ros/ros.h>
#include <geometry_msgs/Twist.h>

int main(int argc, char **argv)
{
	// ROS节点初始化
	ros::init(argc, argv, "velocity_publisher");

	// 创建节点句柄
	ros::NodeHandle n;

	// 创建一个Publisher，发布名为/turtle1/cmd_vel的topic，消息类型为geometry_msgs::Twist，队列长度10
	ros::Publisher turtle_vel_pub = n.advertise<geometry_msgs::Twist>("/turtle1/cmd_vel", 10);

	// 设置循环的频率
	ros::Rate loop_rate(10);

	int count = 0;
	while (ros::ok())
	{
	    // 初始化geometry_msgs::Twist类型的消息
		geometry_msgs::Twist vel_msg;
		vel_msg.linear.x = 0.5;
		vel_msg.angular.z = 0.2;

	    // 发布消息
		turtle_vel_pub.publish(vel_msg);
		ROS_INFO("Publsh turtle velocity command[%0.2f m/s, %0.2f rad/s]", 
				vel_msg.linear.x, vel_msg.angular.z);

	    // 按照循环频率延时
	    loop_rate.sleep();
	}

	return 0;
}
```

修改`CMakeLists.txt`文件，加入以下内容：

```c++
add_executable(velocity_publisher src/velocity_publisher.cpp)
target_link_libraries(velocity_publisher ${catkin_LIBRARIES})
```

在`learning_topic`下执行`catkin_make`命令，编译完成后执行`source devel/setup.bash`加入环境变量；

启动节点：

```shell
$ roscore
$ rosrun turtlesim turtlesim_node
$ rosrun learning_topic velocity_publisher
```

效果如下：

![image-20220128221542372](https://gitee.com/jxprog/PicBed/raw/master/md/2022/01/2022-01-28-221543.png)

### Python实现

代码：

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

# 该例程将发布turtle1/cmd_vel话题，消息类型geometry_msgs::Twist

import rospy
from geometry_msgs.msg import Twist

def velocity_publisher():
	# ROS节点初始化
    rospy.init_node('velocity_publisher', anonymous=True)

	# 创建一个Publisher，发布名为/turtle1/cmd_vel的topic，消息类型为geometry_msgs::Twist，队列长度10
    turtle_vel_pub = rospy.Publisher('/turtle1/cmd_vel', Twist, queue_size=10)

	#设置循环的频率
    rate = rospy.Rate(10) 

    while not rospy.is_shutdown():
		# 初始化geometry_msgs::Twist类型的消息
        vel_msg = Twist()
        vel_msg.linear.x = 0.5
        vel_msg.angular.z = 0.2

		# 发布消息
        turtle_vel_pub.publish(vel_msg)
    	rospy.loginfo("Publsh turtle velocity command[%0.2f m/s, %0.2f rad/s]", 
				vel_msg.linear.x, vel_msg.angular.z)

		# 按照循环频率延时
        rate.sleep()

if __name__ == '__main__':
    try:
        velocity_publisher()
    except rospy.ROSInterruptException:
        pass

```

（`Python2.7`）执行指令：

```shell
$ python locity_publisher.py
```

报错：

>   ImportError: No module named yaml
>
>   ImportError: No module named rospkg
>
>   ......

执行以下指令即可：

```shell
sudo apt-get install python-rosinstall
sudo pip install -U rosinstall
```

## 六、自定义topic消息格式

先在对应功能包下，新建msg文件夹，文件名**不可随意更改！！！**

然后创建对应的`.msg`文件，格式如下：

>   上面指定了变量，下面指定了常量

```text
string name
uint8  age
uint8  sex

uint8 unknown = 0
uint8 male    = 1
uint8 female  = 2
```

然后在`package.xml`中添加功能包依赖：

```xml
  <build_depend>message_generation</build_depend>
  <exec_depend>message_runtime</exec_depend>
```

在`CMakeLists.txt`对应位置（见注释）添加编译选项：

```text
find_package(catkin REQUIRED COMPONENTS
  ...
  message_generation
)

add_message_files(
  FILES
  Person.msg
)

generate_messages(
  DEPENDENCIES
  std_msgs
)

catkin_package(
  ...
  CATKIN_DEPENDS geometry_msgs roscpp rospy std_msgs turtlesim message_runtime
  ...
)
```

最后回到工作区根目录执行`catkin_make`，即可生成对应文件：

-   `.h`文件位于`ws_name/devel/include/pkg_name/`
-   `.py`文件位于`ws_name/devel/lib/python2.7/dist-packages/pkg_name/msg`
