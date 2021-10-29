## 一、软硬件版本

|      名称      |                          版本                           |
| :------------: | :-----------------------------------------------------: |
|    系统版本    | Ubuntu 18.04.6 LTS (GNU/Linux 4.15.0-48-generic x86_64) |
|     Fabric     |                          2.3.3                          |
|       go       |                  go1.16.9 linux/amd64                   |
|     docker     |                        20.10.10                         |
| docker-compose |                 1.29.2, build 5becea4c                  |

## 二. 安装 Git 和 Curl

```bash
apt update
apt install git
apt install curl
```

## 三. 安装 Golang

```bash
apt install libtool libltdl-dev
wget https://studygolang.com/dl/golang/go1.16.9.linux-amd64.tar.gz
tar -C /usr/local -zxvf  go1.16.9.linux-amd64.tar.gz

vim ~/.profile

export PATH=$PATH:/usr/local/go/bin
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
export PATH=$PATH:$GOPATH/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin

source ~/.profile

go version
# go version go1.17.2 linux/amd64
```

## 四. 安装 Docker

```bash
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun

mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://1on9ula1.mirror.aliyuncs.com"]
}
EOF
systemctl daemon-reload
systemctl restart docker
```

## 五. 安装 Docker-Compose

```bash
apt install python-pip

curl -L "https://get.daocloud.io/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

docker-compose version
# docker-compose version 1.29.2, build 5becea4c
# docker-py version: 5.0.0
# CPython version: 3.7.10
# OpenSSL version: OpenSSL 1.1.0l  10 Sep 2019
```

 ## 六. 安装 Hyperledger fabric

```bash
cd ~

# ---------------------------------------------------
#    准备阶段
# ---------------------------------------------------
git clone https://github.com/hyperledger/fabric.git
cd fabric/scripts/
git clone -b main https://github.com/hyperledger/fabric-samples.git

# 修改一下bootstrap.sh：download部分为以下内容
# download() {
#     echo "==> Done."
# }

cd fabric-samples
# 本机下载并FTP上传以下两个文件
# hyperledger-fabric-ca-linux-amd64-1.5.2.tar.gz 
# hyperledger-fabric-linux-amd64-2.3.3.tar.gz 
# 解压
tar -xzvf hyperledger-fabric-ca-linux-amd64-1.5.2.tar.gz
tar -xzvf hyperledger-fabric-linux-amd64-2.3.3.tar.gz

mkdir -p $GOPATH/src/github.com/hyperledger/
cd $GOPATH/src/github.com/hyperledger/

# ---------------------------------------------------
#    直接复制准备好的文件
# ---------------------------------------------------
cp -r ~/fabric/ ./
cd fabric/scripts/
./bootstrap.sh 

cd fabric-samples/test-network
./network.sh up 
```

## 七. 查看运行的fabric容器

```bash
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Names}}\t{{.Status}}"

docker ps -a --format "table {{.Image}}\t{{.Command}}\t{{.Names}}\t{{.Status}}"

docker ps -a --format "table {{.Command}}\t{{.Names}}\t{{.Status}}\t{{.Ports}}"
```

## ==八. 看到这个就成功了！！！==

![sucess](https://gitee.com/jxprog/PicBed/raw/master/md/2021/10/29-223321.png)

------

参考文章：[Hyperledger fabric 2.3基本环境安装 - linux](https://blog.csdn.net/weixin_44142032/article/details/110230668)

## 九、遇到的问题

1.   参考文章：[Hyperledger Fabric1.4环境搭建](https://www.cnblogs.com/cbkj-xd/p/11067790.html)

     ==error==

     运行`./byfn.sh up`时

     遇到`Could not resolve host: nexus.hyperledger.org`错误

     ==reason== 

     `nexus.hyperledger.org`这个网站不再维护了

2.   参考文章：[Hyperledger Fabric1.0环境搭建](https://blog.csdn.net/qq_36336522/article/details/84071711)

     ==error==

      运行`source download-dockerimages.sh -c x86_64-1.0.0 -f x86_64-1.0.0`时

     遇到错误：

     ```text
     Cannot run peer because cannot init crypto, missing /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp folder 
     ```
     ==reason==

     1.   参考 [fabric部署e2e_cli单节点和first-network示例及相关错误](https://blog.csdn.net/vivian_ll/article/details/79966210) 文章
     2.   查看`fabric/release/linux-amd64/bin`文件夹，发现文件夹下无内容，也没有`crypto、configtxgen`等文件，可能原因是`configtxgen`版本不一致导致的。
     3.   尝试手动运行`bootstrap.1.0.0.sh`下载
     4.   发现同上面一样的错误`Could not resolve host: nexus.hyperledger.org`
     5.   所以最后的原因还是nexus.hyperledger.org`这个网站不再维护了

<div style="text-align:center;margin-top:18px;"><span style="color:crimson;font-size:27px">所以2.X版本才是最终解 ！！！</span></div>

