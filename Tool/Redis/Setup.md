# 安装Redis

[TOC]

## 一、下载安装

``````shell
wget http://download.redis.io/redis-stable.tar.gz
tar -xzf redis-stable.tar.gz
cd redis-stable/
make
...
``````

```shell
# 以下命令将Redis常用命令加入/usr/local/bin
make install

# 输出
cd src && make install
make[1]: Entering directory '/root/redis-stable/src'

Hint: It's a good idea to run 'make test' ;)

    INSTALL redis-server
    INSTALL redis-benchmark
    INSTALL redis-cli
make[1]: Leaving directory '/root/redis-stable/src'
```

## 二、测试安装

```shell
make test

# 输出摘录
cd src && make test
make[1]: Entering directory '/root/redis-stable/src'
Cleanup: may take some time... OK
Starting test server at port 21079
[ready]: 26372
Testing unit/printver
[ready]: 26374
Testing unit/dump
...
[ready]: 26386
Testing unit/type/hash
[ready]: 26387
Testing unit/type/stream
[ok]: LPOS basic usage
...
[1/64 done]: unit/printver (1 seconds)
...
[64/64 done]: defrag (142 seconds)

                   The End

Execution time of different units:
  1 seconds - unit/printver
  1 seconds - unit/type/incr
  ...
  360 seconds - integration/replication
  142 seconds - defrag

\o/ All tests passed without errors!

Cleanup: may take some time... OK
make[1]: Leaving directory '/root/redis-stable/src'
```

## 三、基本配置

**直接启动**`Redis`会有三个**Warning**：

-   Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
-   WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
-   WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.

如下所示：

```shell
root@iZwz91577x7sn2pgjejf5kZ:~# redis-server
20060:C 02 Dec 2021 18:32:07.848 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
20060:C 02 Dec 2021 18:32:07.850 # Redis version=6.2.6, bits=64, commit=00000000, modified=0, pid=20060, just started
20060:C 02 Dec 2021 18:32:07.850 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
20060:M 02 Dec 2021 18:32:07.869 * monotonic clock: POSIX clock_gettime
                _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis 6.2.6 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 20060
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           https://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'

20060:M 02 Dec 2021 18:32:07.892 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
20060:M 02 Dec 2021 18:32:07.893 # Server initialized
20060:M 02 Dec 2021 18:32:07.893 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
20060:M 02 Dec 2021 18:32:07.904 * Ready to accept connections
```

>   1.   第一个警告：
>        使用`redis-server /path/to/redis.conf`指定配置文件路径即可
>   2.   第二个警告：
>        -   临时解决方案：输入`echo 511 > /proc/sys/net/core/somaxconn`
>        -   永久解决方案：将`net.core.somaxconn = 1024`添加到`/etc/sysctl.conf`中，然后执行`sysctl -p`生效配置
>   3.   第三个警告：
>        将`vm.overcommit_memory = 1`添加到`/etc/sysctl.conf`中，然后执行`sysctl -p`生效配置

**最终效果**（暂时仍采用默认配置）如下所示：

```shell
root@iZwz91577x7sn2pgjejf5kZ:~# redis-server
26080:C 02 Dec 2021 18:41:40.215 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
26080:C 02 Dec 2021 18:41:40.215 # Redis version=6.2.6, bits=64, commit=00000000, modified=0, pid=26080, just started
26080:C 02 Dec 2021 18:41:40.215 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
26080:M 02 Dec 2021 18:41:40.215 * monotonic clock: POSIX clock_gettime
                _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis 6.2.6 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 26080
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           https://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'

26080:M 02 Dec 2021 18:41:40.217 # Server initialized
26080:M 02 Dec 2021 18:41:40.217 * Loading RDB produced by version 6.2.6
26080:M 02 Dec 2021 18:41:40.217 * RDB age 25 seconds
26080:M 02 Dec 2021 18:41:40.217 * RDB memory usage when created 0.83 Mb
26080:M 02 Dec 2021 18:41:40.217 # Done loading RDB, keys loaded: 0, keys expired: 0.
26080:M 02 Dec 2021 18:41:40.217 * DB loaded from disk: 0.000 seconds
26080:M 02 Dec 2021 18:41:40.217 * Ready to accept connections
```
