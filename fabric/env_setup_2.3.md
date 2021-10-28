## 1. 安装 Git 和 Curl

```bash
apt update
apt install git
apt install curl
```

## 2. 安装 Golang

```bash
apt install libtool libltdl-dev
wget https://studygolang.com/dl/golang/go1.17.2.linux-amd64.tar.gz
tar -C /usr/local -zxvf  go1.17.2.linux-amd64.tar.gz

vim ~/.profile

export PATH=$PATH:/usr/local/go/bin
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
export PATH=$PATH:$GOPATH/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin

source ~/.profile
go version
```

## 3. 安装 Docker

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

## 4. 安装 Docker-Compose

```bash
apt install python-pip

curl -L "https://get.daocloud.io/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
docker-compose version
```

 ## 5. 安装 Hyperledger fabric

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

## 6. 查看运行的fabric容器

```bash
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Names}}\t{{.Status}}"

docker ps -a --format "table {{.Image}}\t{{.Command}}\t{{.Names}}\t{{.Status}}"

docker ps -a --format "table {{.Command}}\t{{.Names}}\t{{.Status}}\t{{.Ports}}"
```

## ==7. 看到这个就成功了！！！==

![image-20211027212746573](D:\TASK\Language\Notes\Archieve\assets\img\image-20211027212746573.png)

------

参考文章：[Hyperledger fabric 2.3基本环境安装 - linux](https://blog.csdn.net/weixin_44142032/article/details/110230668)

