# NS-3 集成 Gym

[TOC]

## 一、介绍

>   [OpenAI Gym](https://gym.openai.com/) is a toolkit for reinforcement learning (RL) widely used in research. The network simulator [ns–3](https://www.nsnam.org/) is the de-facto standard for academic and industry studies in the areas of networking protocols and communication technologies. ns3-gym is a framework that integrates both OpenAI Gym and ns-3 in order to encourage usage of RL in networking research.

OpenAI Gym 是广泛应用于研究的强化学习工具包。网络模拟器 ns-3 是网络协议和通信技术领域学术和工业研究的事实标准。[ns3-gym](https://github.com/tkn-tub/ns3-gym) 是一个集成了 OpenAI Gym 和 ns-3 的框架，旨在鼓励在网络研究中使用 RL。

注意仅适用于`NS-3.29`，使用`app`分支，而不是`app-ns-3.36+`

## 二、安装依赖

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
sudo apt -y install libzmq5 libzmq3-dev libprotobuf-dev protobuf-compiler pkg-config
```

## 三、编译测试

克隆仓库

```sh
cd ./contrib # ns-3.29 目录下已有，无需 mkdir
git clone https://github.com/tkn-tub/ns3-gym.git ./opengym # 目录名称必须是 opengym
cd opengym/
git checkout app # 注意使用 app 分支，默认是 app-ns-3.36+ 分支
```

回到根目录，重新配置并编译`NS-3`项目

```sh
cd ../..

./waf clean
./waf configure --build-profile=optimized
./waf build
```

安装`ns3-gym`模块

```sh
cd ./contrib/opengym/
pip3 install ./model/ns3gym
```

测试案例脚本

```sh
cd ./examples/opengym/ 
./simple_test.py
```

