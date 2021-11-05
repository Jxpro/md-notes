## 一、下载 [Clash](https://github.com/Dreamacro/clash/releases)

-   下载时需注意服务器位数是64还是32位，本文档中使用的64位

![img](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/04-211237.png)

## 二、配置clash

-   命令执行过程（仅供参考,请按本地设置稍作调整）

```bash
gunzip clash-linux-amd64-v1.7.1.gz
mkdir /opt/clash
mv clash-linux-amd64-v1.7.1 /opt/clash/clash

cd /opt/clash/
chmod +x clash
# 上传或下载订阅文件并命名为config.yaml
# 上传或下载Country.mmdb 
# https://www.sub-speeder.com/client-download/Country.mmdb

# 启动clash的指令，需要先配置代理才生效
./clash -d .
```

## 三、设置系统代理

```bash
# 根据需要时开启系统代理，重启会失效
export http_proxy=http://127.0.0.1:7890
export https_proxy=https://127.0.0.1:7890
# 使用socks5协议，通过curl可以获取更多内容，但是wget会出错
#export http_proxy=socks5://127.0.0.1:7890
#export https_proxy=socks5://127.0.0.1:7890

# 关闭系统代理unset http_proxy
unset http_proxy
unset https_proxy

# 直接配置profile，永久设置代理，不建议，取消时需要重启
vim ~/.profile
export http_proxy=http://127.0.0.1:7890
export https_proxy=https://127.0.0.1:7890
source ~/.profile
```

## 四、配置为服务，并开机自启

1.   创建service文件

```bash
vim /etc/systemd/system/clash.service
```

2.   填入以下内容**(注意修改clash文件夹路径)**

```bash
[Unit]
Description=clash daemon

[Service]
Type=simple
User=root
ExecStart=/opt/clash/clash -d /opt/clash/
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

3.   重新加载systemctl daemon

```
systemctl daemon-reload
```

4.   启动Clash

```
systemctl start clash.service
```

5.   设置Clash开机自启动

```
systemctl enable clash.service
```

## 五、测试验证

-   通过curl命令访问www.google.com.hk，响应正常

-   ```bash
    curl www.google.com.hk
    ```

![image-20211104221817997](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/04-221819.png)

## 六、配置定时更新订阅

-   Clash For Linux 到目前为止没有自动订阅方式，我们做一个计划任务实现更新`config.yaml`
-   用Cron执行计划任务

```text
[root@localhost ~]# crontab -e
```

-   填入以下内容

```text
29 6    * * *   root    pgrep clash | xargs kill -s 9 
30 6    * * *   root    mv /opt/clash/config.yaml /opt/clash/configbackup.yaml 
31 6    * * *   root    wget -P /opt/clash/ -O config.yaml [你的订阅链接]
32 6    * * *   root    nohup /opt/clash/clash -d /opt/clash/
```

-   按Esc和:wq保存退出
-   重启crontab，使配置生效

```text
[root@localhost ~]# systemctl restart crond.service
```

## 七、参考链接

-   [Linux下安装&配置Clash以实现代理上网](https://zhuanlan.zhihu.com/p/369344633)
-   [Clash-Linux-折腾笔记](https://github.com/yuanlam/Clash-Linux#enjoy)
-   [Linux安装Clash代理](https://www.cnblogs.com/lfri/p/15265108.html)