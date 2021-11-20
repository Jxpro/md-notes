# 查看电脑连接的wifi密码

打开`CMD`（`Terminal`不行），输入以下指令，查看电脑连接过的WiFi名称

```bash
netsh wlan show profiles
```

![list](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/17-220534.png)

输入以下指令，查看该WiFi名称的详细信息，如下图所示：

```bash
netsh wlan show profiles WiFi名称 key=clear
```

![cont](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/17-220817.png)

提示：如果WiFi名称为汉字不能输入，可以在其它地方输入后复制粘贴。