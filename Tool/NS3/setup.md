# 安装 NS-3

[TOC]

## 一、介绍

>   **ns-3** is a discrete-event network simulator for Internet systems, targeted primarily for research and educational use. ns-3 is free, open-source software, licensed under the GNU GPLv2 license, and maintained by a worldwide community.

ns-3 是一款用于互联网系统的离散事件网络模拟器，主要针对研究和教育用途。ns-3 是一款免费的开源软件，采用 GNU GPLv2 许可，由全球社区共同维护。

## 二、版本要求

推荐系统版本：Ubuntu 20.04 LTS（推荐），或者 Ubuntu 22.04 LTS

NS-3 版本：仅适用于 3.29 版本，3.36 后的版本变化较大

## 三、安装依赖

设置代理

```sh
export all_proxy=http://172.20.160.1:7890
```

更新源

```sh
sudo apt update
```

系统依赖

```sh
sudo apt install -y g++ python3 cmake ninja-build git gir1.2-goocanvas-2.0 python3-gi python3-gi-cairo python3-pygraphviz gir1.2-gtk-3.0 ipython3 tcpdump wireshark sqlite sqlite3 libsqlite3-dev qtbase5-dev qtchooser qt5-qmake qtbase5-dev-tools openmpi-bin openmpi-common openmpi-doc libopenmpi-dev doxygen graphviz imagemagick python3-sphinx dia texlive dvipng latexmk texlive-extra-utils texlive-latex-extra texlive-font-utils libeigen3-dev gsl-bin libgsl-dev libgslcblas0 libxml2 libxml2-dev libgtk-3-dev lxc lxc-utils lxc-templates vtun uml-utilities ebtables bridge-utils libboost-all-dev python-is-python3 ccache clang-format clang-tidy gdb valgrind python3-pip python3-setuptools mercurial unzip
```

Python 依赖

```sh
python3 -m pip install kiwi cppyy==2.4.2 protobuf==3.20.3
pip3 install kiwi cppyy==2.4.2 protobuf==3.20.3
pip install kiwi cppyy==2.4.2 protobuf==3.20.3

echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

## 四、编译测试

下载源码

```sh
wget https://www.nsnam.org/releases/ns-allinone-3.29.tar.bz2
```

解压源码

```sh
tar xjf ns-allinone-3.29.tar.bz2
```

编译软件

```sh
cd ns-allinone-3.29
./build.py --enable-examples --enable-tests
```

测试用例

```sh
cd ns-3.29
./test.py
```

运行 `Hello World`

```sh
./waf --run hello-simulator
```
