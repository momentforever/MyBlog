# K8S部署

## 环境初始化

### 检查操作系统版本

> 大于 centos 7.5

### 主机名解析

```powershell
# /etc/hosts
192.168.123.104 master
192.168.123.105 node1
192.168.123.106 node2
```

### 时间同步

```powershell
# 如未安装，则安装该服务
[root@all ~]# yum install chrony -y

[root@all ~]# systemctl start chronyd

[root@all ~]# systemctl enable chronyd

# 验证时间
[root@all ~]# date
```

### 禁用iptables和firewalld

```powershell
# 关闭firewalld
[root@all ~]# systemctl stop firewalld
[root@all ~]# systemctl disable firewalld
# 关闭iptables
[root@all ~]# systemctl stop iptables
[root@all ~]# systemctl disable iptables
```

### 禁用selinux

```powershell
# /etc/selinux/config
SELINUX=disable

# 可最后重启
[root@all ~]# reboot
```

### 禁用swap分区

```powershell
# 注释swap分区一行
# /etc/fstab
# /dev/mapper/centos-swap swap          swap    defaults    0 0 

# 可最后重启
[root@all ~]# reboot
```

### 修改linux的内核参数

```powershell
# /etc/sysctl.d/kubernetes.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.brifge-nf-call-iptables = 1
net.ipv4.ip_forward = 1

# 重载配置
[root@all ~]# sysctl -p

# 加载网桥过滤模块
[root@all ~]# modprobe br_netfilter

# 查看网桥过滤模块是否加载成功
[root@all ~]# lsmod | grep br_netfilter
br_netfilter           22256  0 
bridge                151336  1 br_netfilter
```

### 配置ipvs

```powershell
# 安装ipset和ipvsadm
[root@all ~]# yum install ipset ipvsadm -y

# 添加需要加载的模块写入脚本文件
[root@all ~]# cat <<EOF > /etc/sysconfig/modules/ipvs.modules
#! /bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
EOF

# 为脚本文添加执行权限
[root@all ~]# chmod +x /etc/sysconfig/modules/ipvs.modules

# 执行脚本文件
[root@all ~]# /bin/bash /etc/sysconfig/modules/ipvs.modules

# 查看对应的模块是否加载成功
[root@all ~]# lsmod | grep -e ip_vs -e nf_conntrack_ipv4
nf_conntrack_ipv4      15053  0 
nf_defrag_ipv4         12729  1 nf_conntrack_ipv4
ip_vs_sh               12688  0 
ip_vs_wrr              12697  0 
ip_vs_rr               12600  0 
ip_vs                 145497  6 ip_vs_rr,ip_vs_sh,ip_vs_wrr
nf_conntrack          139224  2 ip_vs,nf_conntrack_ipv4
libcrc32c              12644  3 xfs,ip_vs,nf_conntrack
# 可最后重启
[root@all ~]# reboot
```

### 重启

```powershell
[root@all ~]# reboot
```

## 集群所需组件安装

### 安装docker

```powershell
# 切换镜像源
[root@all ~]# wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo

# 查看当前镜像源中支持的docker版本
[root@all ~]# yum list docker-ce --showduplicates

# 安装特定版本的docker-ce
# 必须制定--setopt=obsoletes=0，否则yum会自动安装更高版本
[root@all ~]# yum install --setopt=obsoletes=0 docker-ce-18.06.3.ce-3.el7 -y

# 添加一个配置文件
# Docker 在默认情况下使用Vgroup Driver为cgroupfs，而Kubernetes推荐使用systemd来替代cgroupfs
[root@all ~]# mkdir /etc/docker
[root@all ~]# cat <<EOF> /etc/docker/daemon.json
{
	"exec-opts": ["native.cgroupdriver=systemd"],
	"registry-mirrors": ["https://kn0t2bca.mirror.aliyuncs.com"]
}
EOF

# docker开机启动
[root@all ~]# systemctl restart docker
[root@all ~]# systemctl enable docker
```

### 安装Kubernetes组件
```powershell
# 由于kubernetes的镜像在国外，速度比较慢，这里切换成国内的镜像源
# /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgchech=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
       http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg

# 安装kubeadm,kubelet和kubectl
[root@all ~]# yum install --setopt=obsoletes=0 kubeadm-1.17.4-0 kubelet-1.17.4-0 kubectl-1.17.4-0 -y

# 配置kubelet的cgroup
# /etc/sysconfig/kubelet
KUBELET_CGROUP_ARGS="--cgroup-driver=systemd"
KUBE_PROXY_MODE="ipvs"

# kubelet开机启动
[root@all ~]# systemctl enable kubelet
```
## 集群安装


### 准备集群镜像
```powershell
# 在安装kubernetes集群之前，必须要提前准备好集群需要的镜像，所需镜像可以通过下面命令查看
[root@all ~]# kubeadm config images list

# 下载镜像

# ./k8s_mirror.sh
images=(
	kube-apiserver:v1.17.4
	kube-controller-manager:v1.17.4
	kube-scheduler:v1.17.4
	kube-proxy:v1.17.4
	pause:3.1
	etcd:3.4.3-0
	coredns:1.6.5
)

for imageName in ${images[@]};do
	docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/$imageName
	docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/$imageName k8s.gcr.io/$imageName
	docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/$imageName 
done

[root@all ~]# chmod 777 -R k8s_mirror.sh
[root@all ~]# ./k8s_mirror.sh
```


### 集群初始化

+ ``master``节点执行

```powershell
# 创建集群
# 只需要修改apiserver-advertise-address为自身IP
[root@master ~]# kubeadm init \
	--apiserver-advertise-address=192.168.123.104 \
	--image-repository registry.aliyuncs.com/google_containers \
	--kubernetes-version=v1.17.4 \
	--service-cidr=10.96.0.0/12 \
	--pod-network-cidr=10.244.0.0/16
# 创建必要文件
[root@master ~]# mkdir -p $HOME/.kube
[root@master ~]# sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
[root@master ~]# sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

+ ``node``节点执行

```powershell
# 将node节点加入集群，根据master生成不同配置
[root@node ~]# kubeadm join 192.168.123.104:6443 --token uh6669.lcersfp33tzkeelc \
    --discovery-token-ca-cert-hash sha256:cf172bd76976155749a070f19dee15e64def11637621884ca9517285451fec7a
```

+ ``master``节点执行

```powershell
# 查看node是否加入集群
[root@master ~]# kubectl get nodes
NAME     STATUS     ROLES    AGE    VERSION
master   NotReady   master   4m4s   v1.17.4
node1    NotReady   <none>   5s     v1.17.4
node2    NotReady   <none>   6s     v1.17.4
```

### 安装网络插件
> kubernetes支持多种网络插件
> + flannel(本次选择)
> + calico
> + canal

+ ``master``节点执行，插件使用DaemonSet控制器，会在每个节点上运行

```powershell
[root@master ~]# wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

# 如果无法下载，可修改镜像源

# 使用配置文件启用fannel
[root@master ~]# kubectl apply -f kube-flannel.yml

# 稍等片刻，再次查看集群节点状态
[root@master ~]# kubectl get nodes
NAME     STATUS   ROLES    AGE   VERSION
master   Ready    master   21m   v1.17.4
node1    Ready    <none>   17m   v1.17.4
node2    Ready    <none>   17m   v1.17.4
```

## 服务部署

```powershell
# 部署nginx
[root@master ~]# kubectl create deployment nginx  --image=nginx:1.14-alpine

# 暴露端口
[root@master ~]# kubectl expose deploy nginx  --port=80 --target-port=80  --type=NodePort

# 查看服务
[root@master ~]# kubectl get pod,svc
NAME                         READY   STATUS    RESTARTS   AGE
pod/nginx-6867cdf567-nlfbs   1/1     Running   0          103s

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        30m
service/nginx        NodePort    10.104.231.42   <none>        80:32471/TCP   87s
```