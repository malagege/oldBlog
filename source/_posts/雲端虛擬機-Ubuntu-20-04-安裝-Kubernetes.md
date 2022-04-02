---
title: 雲端虛擬機 Ubuntu 20.04 安裝 Kubernetes
date: 2022-04-02 23:58:27
tags: [kubernetes]
categories: Kubernetes
---

之前都是在VM測試用Ubuntu 18.04 快速用 apt 安裝，但發現 20.04 沒有這麼順利，所以這邊就簡單照網路操作安裝，順便紀錄一下。

<!--more-->

## 安裝 K8S

###  安裝kubelet, kubeadm and kubectl

```bash=
sudo apt update
sudo apt -y install curl apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list


sudo apt update
sudo apt -y install vim git curl wget kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

#### 確認結果

```bash=
kubectl version --client && kubeadm version
```

![](https://i.imgur.com/QJ6RzSx.png)


### 關掉swap

```bash=
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
sudo swapoff -a
```

###  設定 kernel modules


```bash=
# Enable kernel modules
sudo modprobe overlay
sudo modprobe br_netfilter

# Add some settings to sysctl
sudo tee /etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

# Reload sysctl
sudo sysctl --system
```

#### 確認執行結果

```bash=
lsmod | grep br_netfilter
```

### 安裝 Container runtime

#### docker

```bash=
# Add repo and Install packages
sudo apt update
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
```

```bash=
## 上下面這段要分開複製執行
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install -y containerd.io docker-ce docker-ce-cli
```
```bash=
# Create required directories
sudo mkdir -p /etc/systemd/system/docker.service.d

# Create daemon json config file
sudo tee /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

# Start and enable Services
sudo systemctl daemon-reload 
sudo systemctl restart docker
sudo systemctl enable docker
```

### 啟用 kubelet


```bash=
sudo systemctl enable kubelet
```


### pull 相關 kubernetes image 元件

```bash=
sudo kubeadm config images pull
```

### kubernetes-master 初始化 kubernetes 網路

**kubernetes-master 需要做，worker跳過**
**kubernetes-master 需要做，worker跳過**
**kubernetes-master 需要做，worker跳過**

```
# calic 的網路範圍，但我後來失敗改用flannel
# sudo kubeadm init \
  --pod-network-cidr=192.168.0.0/16
# flannel IP範圍是  10.244.0.0 /16
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

> The screenshot below shows that the initialization was successful. We have also added a flag to specify the pod network with the IP 10.244.0.0, It’s the default IP that the kube-flannel uses. We will discuss more on the pod network in the next step.



這邊有失敗的話，可以執行`kubeadm reset`重設定。再重新執行

參考:[初始化 Kubernetes 问题（端口占用）_u013004700的专栏-CSDN博客_k8s端口占用](https://blog.csdn.net/u013004700/article/details/81326706)


結果下面會顯示

```
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

kubeadm join 192.168.100.11:6443 --token a3egpo.22u4srzgnrmoaxfe \
        --discovery-token-ca-cert-hash sha256:ca6ab702a4247c0d7dd0daf040e9c53d90b8e5c3085dada496bec7ec4e98ed55 
```

### kubectl 連線設定

kubectl 連線設定套用
```bash=
mkdir -p $HOME/.kube
sudo cp -f /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```



可以看到安裝完某些服務`Pendding`
![](https://i.imgur.com/vrlSbBn.png)

### 查加 worker1 指令

忘記怎麼加可以下

```
kubeadm token create --print-join-command
```

會顯示加 worker指令

![](https://i.imgur.com/pU1ikYU.png)

接下來要加 worker 指令


### 加入 bash auto-completion 

```bash=
echo 'source <(kubectl completion bash)' >>~/.bashrc
```
重新 ssh 進來就可以正常使用。

參考:[bash auto-completion on Linux | Kubernetes](https://kubernetes.io/docs/tasks/tools/included/optional-kubectl-configs-bash-linux/)

### 怎麼移除 worker

#### Master 

```bash=
# 停用 node 掉度
kubectl drain k8s-worker1 --delete-local-data --force --ignore-daemonsets
# 刪掉 node(worker)
kubectl delete node k8s-worker1
```

#### Worker

```
sudo kubeadm reset
```

參考:
- [kubernetes集群新增/移除work節點_change the door-CSDN博客_k8s增加work節點](https://blog.csdn.net/javahuazaili/article/details/114197605)
- [向Kubernetes集群添加／刪除Node - IT閱讀](https://www.itread01.com/content/1525714713.html)




### 安裝 K8S 網路套件(calic)  

建議使用IP 範圍選172開頭，我最後選擇flannel ，跑網路這一快才成功。因為我IP是10開頭，不確定是不是因為這樣撞到IP，所以我才換flannel。

```bash=
kubectl create -f https://docs.projectcalico.org/manifests/tigera-operator.yaml 
kubectl create -f https://docs.projectcalico.org/manifests/custom-resources.yaml
```


### flannel 
參考[How to Install and Use Kubernetes on Ubuntu 20.04 • CloudSigma](https://www.cloudsigma.com/how-to-install-and-use-kubernetes-on-ubuntu-20-04/)
```bash=
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/k8s-manifests/kube-flannel-rbac.yml
```


可以查看結果

```bash=
watch kubectl get pods --all-namespaces
```



[部署 Worker 节点-Kubernetes 实践指南（Kubernetes Practice Guide）-面试哥](https://www.mianshigee.com/tutorial/kubernetes-practice-guide/deploy-manual-bootstrapping-worker-nodes.md)


### 也許不需要操作 worker

我剛開始很想在 worker主機看當下主機資源，但操作不能看到，因為之前玩 Docker Swarm 時候我有當下 docker ps 看當下主機資源，後來發現應該要到`control-plane`去操作。

![](https://i.imgur.com/t8WkLdz.png)

其實 join 進去的時候就有寫上。

![](https://i.imgur.com/Rzk1l9i.png)



## 彩蛋

[kubernetes部署loki日志系统_willblog-CSDN博客](https://blog.csdn.net/networken/article/details/120491802)
[從0到1的雲端歷程 ｜2021 AWS CCP_Cloud Practitioner 證照準備 | 操作一下](https://medium.com/%E6%93%8D%E4%BD%9C%E4%B8%80%E4%B8%8B/%E5%BE%9E0%E5%88%B01%E7%9A%84%E9%9B%B2%E7%AB%AF%E6%AD%B7%E7%A8%8B-2021-aws-cloud-practitioner-%E8%AD%89%E7%85%A7%E6%BA%96%E5%82%99-6de1870bdbd4)
[一文搞懂Kubernetes网络策略(上) - 知乎](https://zhuanlan.zhihu.com/p/343868046)
[k8s 中如何修改 pod-network-cidr 地址范围_已解决_博问_博客园](https://q.cnblogs.com/q/124871/)
[簡單5步，輕鬆debug K8S服務！ | IT人](https://iter01.com/510519.html)
[Debug Your Kubernetes Service in 5 Easy Steps | by Ram Rai | Better Programming](https://betterprogramming.pub/debug-your-kubernetes-service-in-5-easy-steps-1457974f024c)