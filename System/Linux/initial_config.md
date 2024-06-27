# Linux服务器从零配置

[TOC]

新服务器到手可以先跑个分

```shell
bash <(wget -qO- bash.spiritlhl.net/ecs)
```

有些服务器默认没有开启bbr，使用这个脚本开启

```shell
bash <(curl -L -s https://raw.githubusercontent.com/teddysun/across/master/bbr.sh)
```

## 一、添加用户

添加用户组

```shell
sudo addgroup common
```

添加用户

```shell
sudo adduser --ingroup common user
```

赋予`root`权限

```shell
# 使用 nano 编辑文件
# 直接编辑，完成后按下 Ctrl+O 保存文件，直接 Enter 保存更改到当前文件
# 保存文件后，按下 Ctrl+X 退出
sudo visudo

# visudo 实际编辑了 /etc/sudoers 文件，可以直接使用 vi/vim 编辑
sudo vim /etc/sudoers
```

找到下面一行，进行复制

```ini
root  ALL=(ALL)    ALL
```

粘贴到下一行，并将 `root` 修改为 `$user` 对应的用户名

```ini
user  ALL=(ALL)    NOPASSWD:ALL
```

## 二、修改主机名

查看当前主机信息：

```shell
sudo hostnamectl

 Static hostname: hk-server
       Icon name: computer-vm
         Chassis: vm
      Machine ID: ...
         Boot ID: ...
  Virtualization: kvm
Operating System: Ubuntu 22.04.3 LTS
          Kernel: Linux 5.15.0-83-generic
    Architecture: x86-64
 Hardware Vendor: QEMU
  Hardware Model: Standard PC _i440FX + PIIX, 1996_
```

设置新的主机名

```shell
sudo hostnamectl set-hostname new-hostname
```

修改`hosts`文件

```shell
sudo vim /etc/hosts
```

找到下面一行

```
127.0.0.1   old-hostname
```

将 `old-hostname` 替换为新的主机名

```
127.0.0.1   new-hostname
```

## 三、SSH配置

打开配置文件

```shell
sudo vim /etc/ssh/sshd_config
```

找到下面一行，取消注释，修改为其他端口

```ini
#Port 22
```

继续向下查看，到文件结尾，确认下面两行未注释，用于密码和公钥登录

```ini
PasswordAuthentication yes
PubkeyAuthentication yes # 或者 PubkeyAcceptedKeyTypes=+ssh-rsa
```

重启`ssh`服务

```shell
sudo systemctl restart ssh
```

也可以直接重启服务器，可以使修改的主机名生效

```shell
sudo shutdown -r now
```

使用新用户重新登录，使用`ssh`生成公私钥对

```shell
# 一直回车，保持默认即可
ssh-keygen
```

添加本机公钥至服务器，然后`:wq`保存

```shell
vim ~/.ssh/authorized_keys
```

## 四、Clash

`GitHub`已删库，`archive`地址

```shell
https://www.clash.la/releases/
```

下载`clash`内核

```shell
wget https://down.clash.la/Clash/Core/Releases/clash-linux-amd64-v1.18.0.gz

gunzip clash-linux-amd64-v1.18.0.gz
mv clash-linux-amd64-v1.18.0 clash
chmod u+x clash
```

下载`Country.mmdb`分流文件

```shell
wget https://github.com/Dreamacro/maxmind-geoip/releases
```

下载`clash`节点配置文件

```shell
curl -o config.yaml 'longURL'

# -L 处理重定向
curl -L -o config.yaml 'shortURL'
```

配置`systemd`服务

```shell
sudo mkdir /etc/clash
sudo cp clash /usr/local/bin
sudo cp config.yaml /etc/clash/
sudo cp Country.mmdb /etc/clash/
```

创建配置文件 `sudo vim /etc/systemd/system/clash.service`

```ini
[Unit]
Description=Clash daemon, A rule-based proxy in Go.
After=network.target

[Service]
Type=simple
Restart=always
ExecStart=/usr/local/bin/clash -d /etc/clash

[Install]
WantedBy=multi-user.target
```

开机自启`clash`

```shell
sudo systemctl enable clash
```

启动`clash`

```shell
sudo systemctl start clash
```

查看`clash`日志

```shell
sudo systemctl status clash
```

设置系统代理

```shell
export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7890
```

取消系统代理

```shell
unset http_proxy https_proxy all_proxy
```

有些服务器默认没有开启bbr，使用这个脚本开启

```shell
wget -qO- https://raw.githubusercontent.com/teddysun/across/master/bbr.sh | sudo bash 2>/dev/null
```

## 五、Application

常用软件包

```shell
sudo apt update
sudo apt install -y lrzsz git zsh nginx socat autojump fzf net-tools exa fd-find wget build-essential
```

安装`ohmyzsh`和`starship`

```shell
sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
curl -sS https://starship.rs/install.sh | sh
```

安装`omz`插件

```shell
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions
git clone https://github.com/wfxr/forgit.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/forgit
git clone https://github.com/Aloxaf/fzf-tab ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/fzf-tab
```

使用防火墙`ufw`

```shell
sudo apt install -y ufw
sudo ufw enable
sudo ufw allow 22
sudo ufw allow 80
sudo ufw allow 443

sudo systemctl enable ufw
sudo timedatectl set-timezone Asia/Shanghai
```

开发工具`nvm`和`conda`

```shell
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh

~/miniconda3/bin/conda init zsh
~/miniconda3/bin/conda config --set changeps1 False
```

下载`ohmyzsh`和`starship`配置文件

```shell
mkdir ~/.config
curl https://gist.githubusercontent.com/Jxpro/0a5729ac38d4b17a99142c4d71f27d8a/raw/33ef48191f7e245aa3d3b053569b9c8c49207a3c/starship.toml -o ~/.config/starship.toml

mv ~/.zshrc ~/.zshrc.back
curl https://gist.githubusercontent.com/Jxpro/8923b5120fb325b31109ba57868cb306/raw/ed37b15390ab907ae3c73db96e7f21fe9517ceb8/.zshrc -o ~/.zshrc
```

## 六、Docker

官方一键安装

```shell
curl -fsSL https://get.docker.com | sh
```

阿里镜像一键安装（失效，镜像全倒）

```shell
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

阿里镜像手动安装（Ubuntu 14.04/16.04（22.04 warning 但可用），随时可能失效且较慢）

```
# step 1: 安装必要的一些系统工具
sudo apt-get update
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
# step 2: 安装GPG证书
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# Step 3: 写入软件源信息
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
# Step 4: 更新并安装Docker-CE
sudo apt-get -y update
sudo apt-get -y install docker-ce

# 安装指定版本的Docker-CE:
# Step 1: 查找Docker-CE的版本:
# apt-cache madison docker-ce
#   docker-ce | 17.03.1~ce-0~ubuntu-xenial | https://mirrors.aliyun.com/docker-ce/linux/ubuntu xenial/stable amd64 Packages
#   docker-ce | 17.03.0~ce-0~ubuntu-xenial | https://mirrors.aliyun.com/docker-ce/linux/ubuntu xenial/stable amd64 Packages
# Step 2: 安装指定版本的Docker-CE: (VERSION例如上面的17.03.1~ce-0~ubuntu-xenial)
# sudo apt-get -y install docker-ce=[VERSION]

```

官方详细安装（由于`GFW`加强，`download.docker.com`也需要使用镜像）

文档：https://docs.docker.com/engine/install/ubuntu/

```shell
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

# Install using the apt repository
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Configure the registry mirrors
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://mirrors.docker.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```