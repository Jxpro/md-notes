# Android Studio相关

[TOC]

## 一、安装与配置

1.   官网下载[Android Studio](https://developer.android.com/studio)并进行安装，然后打开

2.   提示第一次启动无法访问`Android SDK`，直接`cancel`即可，后面安装会再次下载

     ![image-20220501161242605](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-05-01-161244.png)

3.   进入欢迎页面，直接下一步

4.   安装类型，选择自定义，以修改文件存储目录

5.   然后选择环境UI，SDK的安装位置

6.   然后是模拟器相关设置，通常默认即可

7.   最后确认安装，等待下载完成

## 二、修改.gradle位置

### 2.1 问题

使用`Android Studio`时，默认在C盘用户目录下创建`.gradle`文件夹存放`Gradle`相关文件，一般占用空间较大，但如果在当前项目设置里的`gradle`文件夹路径，每次新建项目后都会重置为C盘下的路径。

下面提供两种解决方案，但是特别注意的是，**自定义的.gradle路径必须要让所有用户都具有读写权限！！！**

### 2.2 方法一

在系统环境变量中添加`GRADLE_USER_HOME`，值为自定义的`.gradle`文件夹路径，修改后最好重启

![image-20220501162655053](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-05-01-162656.png)

### 2.3 方法二

1.   首先关闭当前项目

     ![image-20220501162920046](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-05-01-162922.png)

2.   然后在全局配置里修改gradle路径

     ![image-20220501162947823](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-05-01-162950.png)

     ![image-20220501163013655](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-05-01-163017.png)

## 三、修改AVD文件位置

默认的AVD是下载在`C:\Users\用户名\.android\avd`文件夹下的，一个`AVD`往往占用4G以上的内存空间。

为了释放C盘空间，可以将上述文件夹中的`除ini文件以外`的文件剪贴到电脑的任意位置。

再逐个将`C:\Users\用户名\.android\avd`文件夹里面的`ini文件`，将`path`修改为你自己`avd`存放的路径即可。

>   每一个ini文件对应一个avd文件

## 四、Android SDK Manager 无法启动

可能的问题：

1. `SDK/tools`的问题
2. 排查`find_java.bat`文件
3. 删除 `C:\Windows\system32\`下的`java.exe、javaw.exe、javaws.exe`

### 4.1 问题 1

参考：[android SDK SDK Manager.exe 无法打开，一闪而过最终解决办法](https://blog.csdn.net/wang295689649/article/details/60960953)

方案：到[AndroidDevTools](https://www.androiddevtools.cn/)下载下列压缩包，替换掉`SDK/tools`下的内容即可

![image-20220501160022209](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-05-01-160023.png)

### 4.2 问题2

参考：

[android sdk manager无法启动，一闪而过的解决方案](https://blog.csdn.net/yubin_yubin/article/details/8916389)

[Android SDK总结 SDK Manager无法启动、一闪而过的解决办法](https://blog.csdn.net/hueise_h/article/details/9134237)

方案：到`SDK\tools\lib`下，`cmd`运行`find_java.bat`，看到如下输出：

![image-20220501160300114](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-05-01-160301.png)

说明**jdk版本（或发行商）**存在问题，修改`java_home`的指向，再次运行，输出未空则说明没有问题：

![image-20220501160339195](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-05-01-160340.png)