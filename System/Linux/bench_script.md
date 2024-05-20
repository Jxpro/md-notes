# 服务器性能测试脚本

在`Linux`服务器选购和管理中，性能测试是不可或缺具有非常重要的作用。通过各种脚本工具，我们可以方便快速地评估服务器的整体性能，包括`CPU`、内存、硬盘`IO`以及网络速度等。本文将介绍几种常用的性能测试脚本，帮助大家快速了解并应用于日常服务器管理中。

## ecs

>   该脚本配置修改和依赖性较多，推荐系统初始状态下直接进行测试，不容易破坏现有环境

开源地址：https://github.com/spiritLHLS/ecs

```shell
bash <(wget -qO- bash.spiritlhl.net/ecs)
```

正在维护中的，融合优化了很多个测试脚本的集大成者

脚本功能众多，能想到的基本都有，详情见 [README](https://github.com/spiritLHLS/ecs?tab=readme-ov-file#%E8%9E%8D%E5%90%88%E6%80%AA%E5%8A%9F%E8%83%BD)

## LemonBench

>   简单来说，该脚本比`bench`和`yabs`全面，比`ecs`精简，推荐一般情况下使用

开源地址：https://github.com/LemonBench/LemonBench

```
wget -qO- https://raw.githubusercontent.com/LemonBench/LemonBench/main/LemonBench.sh | bash -s -- --fast
```

有段时间没更新了，但目前可用，测试较为全面，还包含了流媒体检索测试

支持以下项目的基准测试：

-   **系统信息**: 显示详细的系统配置，包括CPU、虚拟化、内存、磁盘和操作系统版本信息。
-   **网络信息**: 显示IPv4和IPv6x信息，包括ip地址、GeoIP，以默认路由网卡为准
-   **流媒体解锁测试**: 检测Netflix、HBO Now、Youtube Premium等流媒体服务的区域解锁状态。
-   **CPU性能测试**: 包含1线程、半线程和全线程的性能测试，检测超线程倍率。
-   **磁盘性能测试**: 使用FIO进行4K和128K写入/读取性能分析。
-   **网络速度测试**: 基于Ookla Speedtest进行多点网络速度测试，包括国内外多个节点，可选快速模式、完整模式。
-   **路由追踪测试**: 使用NextTrace对国内外主要运营商进行路由追踪测试可选快速模式、完整模式。

## bench

开源地址：https://github.com/teddysun/across/blob/master/bench.sh

```shell
wget -qO- bench.sh | bash
```

目前仍在保持更新，且被广泛使用的脚本，简单快速

包括以下几个核心功能：

-   **系统信息**: 自动检测并显示服务器的操作系统、内核、处理器型号和数量等信息。
-   **网络测试**: 利用 `speedtest-cli` 工具测试服务器与多个数据中心的网络连接速度。
-   **硬盘IO测试**: 进行三次硬盘 IO 测试并计算出平均值，以评估硬盘的读写速率。

## yabs

开源地址：https://github.com/masonr/yet-another-bench-script

```shell
wget -qO- yabs.sh | bash
```

同样保持更新，另外使用了`fio`、`iperf3`、`Geekbench`等工具进行性能测试和 CPU 跑分

脚本的特点如下：

-   **系统信息**: 显示详细的系统配置，包括 CPU、内存等。
-   **网络测试**: 同样使用 `iperf3-cli` 进行多点网络速度测试。
-   **硬盘测试**: 使用 `fio` 进行深入的硬盘性能分析。
-   **CPU测试**: 通过 `Geekbench` 对 CPU 进行跑分测试，检测处理能力。

## superspeed

开源地址：https://github.com/Jxpro/superspeed

```
bash <(wget -qO- https://raw.githubusercontent.com/Jxpro/superspeed/master/superspeed.sh)
```

目前已不更新，本人Fork进行了部分代码精简，但由于服务器节点大部分失效，需要更新维护可继续使用。

