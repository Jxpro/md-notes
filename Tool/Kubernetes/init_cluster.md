# Ubuntu 搭建 k8s 集群 ( 公网，详细讲解版 )

[TOC]

## 一、软件版本

|    名称    |                      版本                       |
| :--------: | :---------------------------------------------: |
|   System   | Ubuntu 22.04 LTS (GNU/Linux 5.15.0-107-generic) |
| Kubernetes |                      1.30                       |
|   Docker   |                     27.0.1                      |

## 二、系统设置

### 2.1 SELinux

`SELinux` 是系统安全方面的，其的安全策略可能会干扰容器的正常运行。

`Ubuntu` 没有这个东西可以不管，`CentOS` 执行下面命令关闭 `SELinux` 避免一些权限问题，然后重启系统

```shell
setenforce 0
sudo sed -i 's/^SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
```

### 2.2 DNS

设置主机名

```shell
sudo hostnamectl set-hostname k8s-node1
```

修改 `hosts` 文件，将IP地址和主机名填进去

```shell
sudo tee -a /etc/hosts <<-'EOF'
# k8s cluster
192.168.1.10 k8s-master
192.168.1.20 k8s-node1
192.168.1.30 k8s-node2
EOF
```

### 2.3 内核模块

```shell
sudo tee /etc/modules-load.d/k8s.conf <<-'EOF'
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter
```

### 2.4 防火墙

云服务需要在安全组中操作防火墙

#### 2.4.1 直接关闭防火墙

```shell
ufw disable
```

#### 2.4.2 或者开启相关端口

官方文档：https://kubernetes.io/docs/reference/networking/ports-and-protocols/

-   Control plane

    | Protocol | Direction | Port Range | Purpose                 |
    | -------- | --------- | ---------- | ----------------------- |
    | TCP      | Inbound   | 6443       | Kubernetes API server   |
    | TCP      | Inbound   | 2379-2380  | etcd server client API  |
    | TCP      | Inbound   | 10250      | Kubelet API             |
    | TCP      | Inbound   | 10259      | kube-scheduler          |
    | TCP      | Inbound   | 10257      | kube-controller-manager |

-   Worker node(s)

    | Protocol | Direction | Port Range | Purpose     |
    | -------- | --------- | ---------- | ----------- |
    | TCP      | Inbound   | 10250      | Kubelet API |
    | TCP      | Inbound   | 10256      | kube-proxy  |

-   所有节点（不确定）

    | 协议 | 端口范围 | 作用                   | 使用者       |
    | ---- | -------- | ---------------------- | ------------ |
    | UDP  | 8472     | vxlan Overlay 网络通信 | Overlay 网络 |
    | TCP  | 179      | calico networking      | BGP          |
    | UDP  | 4789     | calico networking      | BGP          |
    | TCP  | 5473     | calico networking      | BGP          |

### 2.5 虚拟内存（默认可用，检查一下）

官方要求关闭 `swap`，开启这个可能会导致 `Kubernetes` 的内存或性能问题，详细参考文章：[因打开了 swap 导致的 Kubernetes 重启后无法自愈问题](https://nolebase.ayaka.io/%E7%AC%94%E8%AE%B0/%F0%9F%A7%B1%20%E5%9F%BA%E7%A1%80%E8%AE%BE%E6%96%BD/%F0%9F%9A%A2%20Kubernetes/%E5%9B%A0%E6%89%93%E5%BC%80%E4%BA%86%20swap%20%E5%AF%BC%E8%87%B4%E7%9A%84%20Kubernetes%20%E9%87%8D%E5%90%AF%E5%90%8E%E6%97%A0%E6%B3%95%E8%87%AA%E6%84%88%E9%97%AE%E9%A2%98.html)

#### 2.5.1 检测

使用 `free -m` 命令来查看 `Swap ` 是否禁用

```shell
free -m
               total        used        free      shared  buff/cache   available
Mem:            1963         287         599           2        1076        1515
Swap:              0           0           0
```

#### 2.5.2 关闭

```shell
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```

### 2.6 时钟同步（默认可用，检查一下）

#### 2.6.1 检测

使用 `timedatectl status` 查看，国内服务器时区一般已配置好 `Asia/Shanghai`

```shell
timedatectl status
               Local time: Wed 2024-06-26 21:24:25 CST
           Universal time: Wed 2024-06-26 13:24:25 UTC
                 RTC time: Wed 2024-06-26 13:24:25
                Time zone: Asia/Shanghai (CST, +0800)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

#### 2.6.2 设置

集群通信需要同一时间来同步行为，国内设置为上海

```shell
timedatectl set-timezone Asia/Shanghai
```

重启时间同步服务

```shell
systemctl restart systemd-timesyncd.service
```

查看时间

```shell
timedatectl status
```

### 2.7 网络配置（默认可用，检查一下）

#### 2.7.1 检测

```shell
sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward
```

像下面这样输出表示全部开启

```shell
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
```

#### 2.7.2 开启

若未开启，则运行下面命令

```shell
sudo tee /etc/sysctl.d/k8s.conf <<-'EOF'
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system
```

### 2.8 IPVS (可选)

安装 `ipset` 和 `ipvsadm`

```bash
sudo apt update && sudo apt install -y ipset ipvsadm
```

加载内核模块

```shell
sudo tee /etc/modules-load.d/ipvs.conf <<-'EOF'
ip_vs
ip_vs_rr
ip_vs_wrr
ip_vs_sh
nf_conntrack
EOF
```

## 三、软件安装

>   `Docker Engine` 没有实现 [CRI](https://kubernetes.io/zh-cn/docs/concepts/architecture/cri/)， 而这是容器运行时在 `Kubernetes `中工作所需要的。 `v1.24` 之前的版本直接集成了 `Docker Engine` 的一个组件，名为 `dockershim`，但现在不再是 `Kubernetes` 的一部分。为此，必须安装一个额外的服务 [cri-dockerd](https://github.com/Mirantis/cri-dockerd)。
>
>   另外也可以使用 [containerd](https://github.com/containerd/containerd) 运行时环境，该组件随 `Docker` 一起安装，但是需要额外的配置文件。

### 3.1 Docker

`Docker ` 官方一键安装

```shell
curl -fsSL https://get.docker.com | sh
```

官方详细安装（由于 `GFW` 加强，`download.docker.com` 也需要使用镜像）

文档：https://docs.docker.com/engine/install/ubuntu/

```shell
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://mirrors.cloud.tencent.com/docker-ce/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://mirrors.cloud.tencent.com/docker-ce/linux/ubuntu/ \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
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

### 3.2 Containerd

所有命令

```shell
sudo containerd config default | sudo tee /etc/containerd/config.toml > /dev/null
sudo sed -i '/registry.k8s.io\/pause/s|3.8|3.9|' /etc/containerd/config.toml
sudo sed -i 's/SystemdCgroup\ =\ false/SystemdCgroup\ =\ true/g' /etc/containerd/config.toml
sudo sed -i 's/config_path\ =\ ""/config_path\ =\ "\/etc\/containerd\/certs.d"/g' /etc/containerd/config.toml
sudo systemctl restart containerd
```

```shell
sudo mkdir -p /etc/containerd/certs.d/docker.io
sudo tee /etc/containerd/certs.d/docker.io/hosts.toml <<-'EOF'
server = "https://docker.io"

[host."https://mirrors.docker.com"]
  capabilities = ["pull", "resolve"]
EOF

sudo mkdir -p /etc/containerd/certs.d/registry-1.docker.io
sudo tee /etc/containerd/certs.d/registry-1.docker.io/hosts.toml <<-'EOF'
server = "https://registry-1.docker.io"

[host."https://mirrors.docker.com"]
  capabilities = ["pull", "resolve"]
EOF

sudo mkdir -p /etc/containerd/certs.d/registry.k8s.io
sudo tee /etc/containerd/certs.d/registry.k8s.io/hosts.toml <<-'EOF'
server = "https://registry.k8s.io"

[host."https://mirrors.k8s.com"]
  capabilities = ["pull", "resolve"]
EOF

sudo mkdir -p /etc/containerd/certs.d/quay.io
sudo tee /etc/containerd/certs.d/quay.io/hosts.toml << 'EOF'
server = "https://quay.io"

[host."https://mirrors.quay.com"]
  capabilities = ["pull", "resolve"]
EOF
```

详细步骤解释：

生成默认配置文件然后进行修改

```shell
sudo containerd config default | sudo tee /etc/containerd/config.toml
# 或者
sudo containerd config default | sudo tee /etc/containerd/config.toml > /dev/null
```

配置 `pause` 镜像，防止 `kubeadm` 的警告

>   detected that the sandbox image "registry.k8s.io/pause:3.9" of the container runtime is inconsistent with that used by kubeadm.It is recommended to use "registry.aliyuncs.com/google_containers/pause:3.9" as the CRI sandbox image.

保持版本不变，仅将 `registry.k8s.io` 修改为 `registry.aliyuncs.com/google_containers`

```shell
sudo sed -i '/sandbox_image/s|registry.k8s.io/pause|registry.aliyuncs.com/google_containers/pause|' /etc/containerd/config.toml
```

或者指定版本，将 `registry.k8s.io/pause:*` 修改为 `registry.aliyuncs.com/google_containers/pause:3.9`

```shell
sudo sed -i 's/sandbox_image\ =.*/sandbox_image\ =\ "registry.aliyuncs.com\/google_containers\/pause:3.9"/g' /etc/containerd/config.toml
```

将 `SystemdCgroup = false` 修改为 `SystemdCgroup = true`

>   根据文档[Container runtimes ](https://kubernetes.io/docs/setup/production-environment/container-runtimes/)中的内容，对于使用 `systemd` 作为 `init system` 的 `Linux` 发行版，使用 `systemd `作为容器的 `cgroup driver` 可以确保服务器节点在资源紧张的情况更加稳定，因此这里配置各个节点上 `containerd` 的 `cgroup driver` 为 `systemd`。

```shell
sudo sed -i 's/SystemdCgroup\ =\ false/SystemdCgroup\ =\ true/g' /etc/containerd/config.toml
```

配置容器镜像加速器，使用 `config_path` 的方式

>   DEPRECATION: The `mirrors` property of `[plugins."io.containerd.grpc.v1.cri".registry]` is deprecated since containerd v1.5 and will be removed in containerd v2.0. Use `config_path` instead.

```shell
sudo sed -i 's/config_path\ =\ ""/config_path\ =\ "\/etc\/containerd\/certs.d"/g' /etc/containerd/config.toml
```

```shell
sudo mkdir -p /etc/containerd/certs.d/docker.io
sudo tee /etc/containerd/certs.d/docker.io/hosts.toml <<-'EOF'
server = "https://docker.io"

[host."https://mirrors.docker.com"]
  capabilities = ["pull", "resolve"]
EOF

sudo mkdir -p /etc/containerd/certs.d/registry-1.docker.io
sudo tee /etc/containerd/certs.d/registry-1.docker.io/hosts.toml <<-'EOF'
server = "https://registry-1.docker.io"

[host."https://mirrors.docker.com"]
  capabilities = ["pull", "resolve"]
EOF

sudo mkdir -p /etc/containerd/certs.d/registry.k8s.io
sudo tee /etc/containerd/certs.d/registry.k8s.io/hosts.toml <<-'EOF'
server = "https://registry.k8s.io"

[host."https://mirrors.k8s.com"]
  capabilities = ["pull", "resolve"]
EOF

sudo mkdir -p /etc/containerd/certs.d/quay.io
sudo tee /etc/containerd/certs.d/quay.io/hosts.toml << 'EOF'
server = "https://quay.io"

[host."https://mirrors.quay.com"]
  capabilities = ["pull", "resolve"]
EOF
```

重启 `containerd` 服务

```shell
sudo systemctl restart containerd
```

### 3.3 Kubernetes

使用阿里云镜像安装 `kubelet`、`kubeadm`、`kubectl`，并使用 `apt-mark` 保持版本，设置 `kubelet` 开机自启

```shell
sudo apt update && sudo apt install -y apt-transport-https

curl -fsSL https://mirrors.aliyun.com/kubernetes-new/core/stable/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://mirrors.aliyun.com/kubernetes-new/core/stable/v1.30/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

加入自动补全到 `.zshrc` 或者 `.bashrc` 文件

```
tee -a .zshrc <<-'EOF'
# k8s completion
source <(kubectl completion zsh)
source <(kubeadm completion zsh)
EOF

tee -a .bashrc <<-'EOF'
# k8s completion
source <(kubectl completion bash)
source <(kubeadm completion bash)
EOF
```

## 四、配置 Master

### 4.1 拉取镜像

初始化前先拉取所需要的镜像，方便查找初始化的 `warning` 或 `error`

```shell
sudo kubeadm config images pull --kubernetes-version=$(kubeadm version -o short)
```

在不同厂商的云服务器之间，我们需要使用公网访问 `k8s `集群中的每个节点，但是网卡上绑定的是内网 `IP`，会导致 `kubeadm init ` 指定 `--apiserver-advertise-address` 为公网 `IP` 时，`etcd` 无法启动，初始化失败。

### 4.2 初始化

所以我们可以手动将公网 `IP` 绑定到一个虚拟网卡上，这样才能成功初始化 `Master` 节点，该配置在重启后会失效，但是用于初始化没问题。

```shell
sudo ip link add veth0 type dummy
sudo ip addr add 47.102.xx.xx/24 dev veth0
sudo ip link set dev veth0 up
```

添加网卡后进行初始化

```shell
sudo kubeadm init \
      --apiserver-advertise-address=47.102.xx.xx \
      --pod-network-cidr=192.168.0.0/16 \
      --service-cidr=10.96.0.0/16 \
      --kubernetes-version=$(kubeadm version -o short)
```

选项含义：

-   `apiserver-advertise-address`（必需）：指定 `API` 服务器向集群外部通告的 `IP` 地址，在集群节点加入时被用来联系 `API `服务器
-   `pod-network-cidr`（必需）：指定 `Pod` 网络的 `CIDR` 范围，表示分配给 `Pod` 的 `IP` 地址范围。这个 `CIDR` 范围用于配置 `Pod` 网络插件，例如 `Calico` 或 `Flannel`
-   `service-cidr`（必需）：指定 `Service `虚拟 `IP` 地址的 `CIDR ` 范围，这个范围内的 `IP ` 地址用于 `Kubernetes` 内部的服务发现和负载均衡
-   `cri-socket`（使用`Docker & CRI`必需）：指定容器运行时接口（ `CRI` ）套接字文件路径
-   `image-repository`：指定使用阿里云的镜像仓库来拉取 `Kubernetes `相关的 `Docker `镜像
-   `kubernetes-version`：推荐明确指定 `Kubernetes ` 版本，以避免使用非预期的版本

### 4.3 重置

重新初始化 `master` 可以使用 `kubeadm reset` 命令

```shell
sudo kubeadm reset
sudo docker system prune --all

rm -rf $HOME/.kube
sudo rm -rf /etc/cni/net.d
sudo rm -rf /etc/kubernetes
sudo rm -rf /var/lib/etcd
sudo rm -rf /var/lib/kubelet
sudo rm -rf /var/lib/dockershim
sudo rm -rf /var/run/kubernetes

sudo systemctl restart docker
sudo systemctl restart kubelet
```

## 五、添加 Worker

### 5.1 获取 token

`Master` 初始化成功出现如下提示

```shell
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

sudo kubeadm join ip:port --token xxxxxx \
        --discovery-token-ca-cert-hash sha256:xxxxxxxxxxxx
```

最后一个命令就是加入 `Worker` 节点的命令，并带有 `token`

```shell
sudo kubeadm join ip:port --token xxxxxx \
        --discovery-token-ca-cert-hash sha256:xxxxxxxxxxxx
```

如果你未保存token，也可以重新生成，并不是影响已经加入过节点的信息。

```shell
sudo kubeadm token create --print-join-command
```

### 5.2 加入集群

我们同样手动将公网 `IP` 绑定到一个虚拟网卡上， `Worker` 节点才能以公网 `IP`加入集群，该配置在重启后失效，但是用于加入集群没问题。

```shell
sudo ip link add veth0 type dummy
sudo ip addr add 150.158.xx.xx/24 dev veth0
sudo ip link set dev veth0 up
```

在 `Worker` 节点上运行命令便加入了集群

```shell
sudo kubeadm join ip:port --token xxxxxx \
        --discovery-token-ca-cert-hash sha256:xxxxxxxxxxxx
```

使用 `kubectl get nodes`，发现节点均为 `NotReady` 状态，所以我们还需要配置网络插件

```shell
NAME         STATUS     ROLES           AGE     VERSION
k8s-master   NotReady   control-plane   4m14s   v1.30.2
k8s-node1    NotReady   <none>          25s     v1.30.2
```

## 六、网络插件 Calico (failed)

在 `Master` 节点中执行命令进行配置

官方文档：https://docs.tigera.io/calico/latest/getting-started/kubernetes/quickstart

### 6.1 创建 tigera-operator

```shell
wget https://raw.staticdn.net/projectcalico/calico/v3.28.0/manifests/tigera-operator.yaml
kubectl create -f tigera-operator.yaml
```

查看容器状态

```shell
kubectl get ns
NAME              STATUS   AGE
default           Active   2m29s
kube-node-lease   Active   2m29s
kube-public       Active   2m29s
kube-system       Active   2m29s
tigera-operator   Active   33s

kubectl get pods -A
NAMESPACE         NAME                                 READY   STATUS    RESTARTS      AGE
kube-system       coredns-7b5944fdcf-ncxml             0/1     Pending   0             2m41s
kube-system       coredns-7b5944fdcf-q55kx             0/1     Pending   0             2m41s
kube-system       etcd-k8s-master                      1/1     Running   0             2m57s
kube-system       kube-apiserver-k8s-master            1/1     Running   0             2m57s
kube-system       kube-controller-manager-k8s-master   1/1     Running   0             2m57s
kube-system       kube-proxy-4pmwz                     1/1     Running   0             2m41s
kube-system       kube-proxy-cdgqd                     1/1     Running   2 (69s ago)   2m39s
kube-system       kube-scheduler-k8s-master            1/1     Running   0             2m57s
tigera-operator   tigera-operator-76ff79f7fd-vsjl7     1/1     Running   0             50s
```

### 6.2 启动 calico

下载或打开配置文件

```shell
wget https://raw.staticdn.net/projectcalico/calico/v3.28.0/manifests/custom-resources.yaml

vim custom-resources.yaml
```

打开下载的配置文件 `custom-resources.yaml`，修改 `cidr`为 `kubeadm init` 时指定的 ` --pod-network-cidr`

```yaml
# This section includes base Calico installation configuration.
# For more information, see: https://docs.tigera.io/calico/latest/reference/installation/api#operator.tigera.io/v1.Installation
apiVersion: operator.tigera.io/v1
kind: Installation
metadata:
  name: default
spec:
  # Configures Calico networking.
  calicoNetwork:
    ipPools:
    - name: default-ipv4-ippool
      blockSize: 26
      cidr: 192.168.0.0/16
      encapsulation: VXLANCrossSubnet
      natOutgoing: Enabled
      nodeSelector: all()

---

# This section configures the Calico API server.
# For more information, see: https://docs.tigera.io/calico/latest/reference/installation/api#operator.tigera.io/v1.APIServer
apiVersion: operator.tigera.io/v1
kind: APIServer
metadata:
  name: default
spec: {}
```

创建容器，等待几分钟，直到所有容器 `Ready`

```shell
kubectl apply -f custom-resources.yaml
```

### 6.3 遇到的问题

发现两个 `calico-node` 一直无法启动

```shell
kubectl get pods -A
NAMESPACE         NAME                                       READY   STATUS    ...
calico-system     calico-kube-controllers-7b8cc9bb64-pqcwz   1/1     Running   ...
calico-system     calico-node-j4jnb                          0/1     Running   ...
calico-system     calico-node-pr72b                          0/1     Running   ...
calico-system     calico-typha-7fc6bcfd8b-xnzbv              1/1     Running   ...
calico-system     csi-node-driver-l8scr                      2/2     Running   ...
calico-system     csi-node-driver-ztpp9                      2/2     Running   ...
kube-system       coredns-7b5944fdcf-cg2hd                   1/1     Running   ...
kube-system       coredns-7b5944fdcf-rdkdx                   1/1     Running   ...
kube-system       etcd-k8s-master                            1/1     Running   ...
kube-system       kube-apiserver-k8s-master                  1/1     Running   ...
kube-system       kube-controller-manager-k8s-master         1/1     Running   ...
kube-system       kube-proxy-7lcr8                           1/1     Running   ...
kube-system       kube-proxy-vb6cc                           1/1     Running   ...
kube-system       kube-scheduler-k8s-master                  1/1     Running   ...
tigera-operator   tigera-operator-76ff79f7fd-6jfmk           1/1     Running   ...
```

使用 `describe` 查看容器状态

```shell
kubectl describe pod calico-node-j4jnb -n calico-system
```

发现无法建立 `BGP` 连接，具体原因是因为其使用的是内网 `IP`（10.0.4.11），导致无法与公网连接

```shell
Warning  Unhealthy  22s  kubelet  (combined from similar events): Readiness probe failed: 2024-06-27 08:09:58.227 [INFO][656] confd/health.go 202: Number of node(s) with BGP peering established = 0
calico/node is not ready: BIRD is not ready: BGP not established with 10.0.4.11
```

但是查看发现其上报了公网 `IP`（150.158.xx.xx）

```shell
kubectl get nodes -o wide
NAME         STATUS   ROLES           AGE   VERSION   INTERNAL-IP     ...
k8s-master   Ready    control-plane   22m   v1.30.2   47.102.xx.xx    ...
k8s-node1    Ready    <none>          22m   v1.30.2   150.158.xx.xx   ...
```

### 6.4 该问题没有解决

查阅资料初步得出的结论是，`calico` 会自动使用第一个网卡上的第一个 `IP` 地址；

测试以后发现，即使我们像初始化 `Master` 一样，使用 `sudo ip add ...` 添加了公网 `IP`；

它也在内网 `IP` 之后，不会被 `calico ` 检测到，导致无法建立 `BGP` 连接，~~暂时未找到解决办法。~~

目前找到了检测公网 `IP`的方法，但是仍然无法建立 `BGP` 连接。

### 6.5 找到指定IP的方法

在下载的配置文件中添加字段 `nodeAddressAutodetectionV4.interface`，使用匹配挂载到公网 `IP` 的虚拟网卡

```yaml
# This section includes base Calico installation configuration.
# For more information, see: https://docs.tigera.io/calico/latest/reference/installation/api#operator.tigera.io/v1.Installation
apiVersion: operator.tigera.io/v1
kind: Installation
metadata:
  name: default
spec:
  # Configures Calico networking.
  calicoNetwork:
    nodeAddressAutodetectionV4:
      interface: veth.*
    ipPools:
    - name: default-ipv4-ippool
      blockSize: 26
      cidr: 192.168.0.0/16
      encapsulation: VXLANCrossSubnet
      natOutgoing: Enabled
      nodeSelector: all()

---

# This section configures the Calico API server.
# For more information, see: https://docs.tigera.io/calico/latest/reference/installation/api#operator.tigera.io/v1.APIServer
apiVersion: operator.tigera.io/v1
kind: APIServer
metadata:
  name: default
spec: {}
```

### 6.6 该问题还没有解决

测试以后发现，不知道为什么，即使通过 `nodeAddressAutodetectionV4` 成功检测到了公网 `IP`；

但是 `calico `仍然无法建立 `BGP` 连接，暂时也未找到解决办法。

```shell
Warning  Unhealthy  2m25s (x2 over 2m26s)  kubelet            Readiness probe failed: calico/node is not ready: BIRD is not ready: Error querying BIRD: unable to connect to BIRDv4 socket: dial unix /var/run/calico/bird.ctl: connect: connection refused
Warning  Unhealthy  119s                   kubelet            Readiness probe failed: 2024-06-27 12:11:38.695 [INFO][267] confd/health.go 202: Number of node(s) with BGP peering established = 0
calico/node is not ready: BIRD is not ready: BGP not established with 150.158.xx.xx
Warning  Unhealthy  89s  kubelet  Readiness probe failed: 2024-06-27 12:12:08.702 [INFO][301] confd/health.go 202: Number of node(s) with BGP peering established = 0
...
```

