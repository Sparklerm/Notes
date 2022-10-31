# 1. 基于Docker安装

## 准备主机

1、准备3台，2G或更大内存，2核或以上CPU，30G以上硬盘 物理机或云主机或虚拟机
2、系统centos 7.x
3、更新yum镜像源（非必须）

```bash
# 备份
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
# 下载新的CentOS-Base.repo 到 /etc/yum.repos.d/
curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
sed -i -e '/mirrors.cloud.aliyuncs.com/d' -e '/mirrors.aliyuncs.com/d' /etc/yum.repos.d/CentOS-Base.repo
# 运行 yum makecache 生成缓存
yum makecache
```

## 主机设置

```bash
# 如果有三台主机则需要分别在不同的主机上设置主机名
# 主节点
hostnamectl set-hostname k8s-master
# 从节点1
hostnamectl set-hostname k8s-node-1
# 从节点2
hostnamectl set-hostname k8s-node-2

# 在所有的节点上执行一下命令，将IP替换为你设定的IP
cat >> /etc/hosts << EOF
192.168.31.11 k8s-master
192.168.31.11 k8s-node-1
192.168.31.11 k8s-node-2
EOF

cat >> /etc/hosts << EOF
192.168.26.137 k8s-master
EOF

# 关闭所有节点的防火墙
systemctl stop firewalld && systemctl disable firewalld

# 在所有节点关闭selinux
sed -i 's/enforcing/disabled/' /etc/selinux/config && setenforce 0

#  在所有节点关闭swap
swapoff -a && sed -ri 's/.*swap.*/#&/' /etc/fstab

# 设置网络
cat <<EOF > /etc/sysctl.d/k8s.conf
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
# 使配置生效
sysctl --system

# 时间同步（所有节点都需要执行）
yum install ntpdate -y && ntpdate time.windows.com

#设置完后重启系统
systemctl reboot
```

## 安装Docker

```bash
# 1. 在线安装
# step 1: 安装必要的一些系统工具
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
# Step 2: 添加软件源信息
sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# Step 3: 更新并安装Docker-CE
sudo yum makecache fast
sudo yum -y install docker-ce-20.10.0

# 2. 离线安装（推荐）
# 1. 上传rpm安装包
yum install -y docker-ce-18.09.0-3.el7.x86_64.rpm
yum install -y docker-ce-cli-18.09.0-3.el7.x86_64.rpm
# 校验安装结果
docker -v

# 配置Docker
# registry-mirrors 为镜像源配置 可自行更换，其他配置为Kubernetes官方配置
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ],
  "registry-mirrors": ["https://3vv421al.mirror.aliyuncs.com"]
}
EOF


sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://3vv421al.mirror.aliyuncs.com"]
}
EOF

# 设置Docker开机自启并启动Docker
sudo systemctl daemon-reload
sudo systemctl start docker && systemctl enable docker
```

## 安装kubelet、kubeadm、kubectl

- kubeadm：用来初始化集群的指令。
- kubelet：在集群中的每个节点上用来启动 pod 和容器等。
- kubectl：用来与集群通信的命令行工具。

```bash
# 添加kubernetes aliyun源
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

# 安装 kubelet、kubeadm、kubectl
yum -y install kubelet-1.18.0 kubeadm-1.18.0 kubectl-1.18.0 --disableexcludes=kubernetes
# 启动 kubelet 并设置开机自启
systemctl enable kubelet && systemctl start kubelet
```

# 部署Kubernetes

## 部署Kubernetes Master

### Master节点初始化

在主节点上执行命令 初始化Master：

```bash
# IP更换为设置的主节点IP ：--apiserver-advertise-address=[192.168.31.11]
kubeadm init --apiserver-advertise-address=192.168.31.11 --image-repository k8s.gcr.io --kubernetes-version v1.18.0 --service-cidr=10.96.0.0/12 --pod-network-cidr=10.244.0.0/16
```

### Master节点初始化后配置kubectl 工具

```bash
# 以下命令在初始化完Master节点后会打印出
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# 测试命令是否可用
kubectl get nodes
```

### 安装 Pod 网络插件

```bash
# 使用官方配置文件安装
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
# 如果以上方式因网络问题失败可尝试下面的方式
# 1. 通过 https://docs.projectcalico.org/manifests/calico.yaml 下载calico.yaml后上传至服务器
# 2. 执行命令
kubectl apply -f /你的路径/calico.yaml
```

## 从节点操作

### 从节点加入集群

>   在此过程中，从节点通过Docker拉去所需镜像，会导致NotReady到Ready的时间不可控，如果出现超长时间无法从NotReady-->Ready，考虑切换Docker源后重试。

```bash
# 该命令在Master节点初始化后会打印
kubeadm join 192.168.31.11:6443 --token 6v0syz.9dd8zz9zf7nslefy \
    --discovery-token-ca-cert-hash sha256:935511119ff4d39bbfe264ec6b8b928a450df01250fd183c5b27cfc94285e3c7
   
# 获取加入命令
kubeadm token create --print-join-command

# Master节点查看从节点连接状态
kubectl get nodes
```



# 部署Kubernetes-Dashboard

## 1. 部署Dashboard

```bash
# 文件内容在下面
kubectl apply -f ./recommended.yaml

# 暴露Dashboard
# kubectl -n kubernetes-dashboard edit svc kubernetes-dashboard
# 修改文件内容 type: ClusterIPs --> type: NodePort
kubectl patch svc kubernetes-dashboard -p '{"spec":{"type":"NodePort"}}' -n kubernetes-dashboard

#删除默认创建的secret
kubectl delete secret kubernetes-dashboard-certs  -n kubernetes-dashboard

#重新创建secret，主要用来指定证书的存放路径
kubectl create secret generic kubernetes-dashboard-certs --from-file=/etc/kubernetes/pki/ -n kubernetes-dashboard

#删除dashboard的pod，主要让它重新运行，加载证书
kubectl delete pod -n kubernetes-dashboard --all

#查看svc
kubectl -n kubernetes-dashboard get svc

# 访问dashboard
https://ip:port
```

```yaml
# Copyright 2017 The Kubernetes Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

apiVersion: v1
kind: Namespace
metadata:
  name: kubernetes-dashboard

---

apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard

---

kind: Service
apiVersion: v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
spec:
  ports:
    - port: 443
      targetPort: 8443
  selector:
    k8s-app: kubernetes-dashboard

---

apiVersion: v1
kind: Secret
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard-certs
  namespace: kubernetes-dashboard
type: Opaque

---

apiVersion: v1
kind: Secret
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard-csrf
  namespace: kubernetes-dashboard
type: Opaque
data:
  csrf: ""

---

apiVersion: v1
kind: Secret
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard-key-holder
  namespace: kubernetes-dashboard
type: Opaque

---

kind: ConfigMap
apiVersion: v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard-settings
  namespace: kubernetes-dashboard

---

kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
rules:
  # Allow Dashboard to get, update and delete Dashboard exclusive secrets.
  - apiGroups: [""]
    resources: ["secrets"]
    resourceNames: ["kubernetes-dashboard-key-holder", "kubernetes-dashboard-certs", "kubernetes-dashboard-csrf"]
    verbs: ["get", "update", "delete"]
    # Allow Dashboard to get and update 'kubernetes-dashboard-settings' config map.
  - apiGroups: [""]
    resources: ["configmaps"]
    resourceNames: ["kubernetes-dashboard-settings"]
    verbs: ["get", "update"]
    # Allow Dashboard to get metrics.
  - apiGroups: [""]
    resources: ["services"]
    resourceNames: ["heapster", "dashboard-metrics-scraper"]
    verbs: ["proxy"]
  - apiGroups: [""]
    resources: ["services/proxy"]
    resourceNames: ["heapster", "http:heapster:", "https:heapster:", "dashboard-metrics-scraper", "http:dashboard-metrics-scraper"]
    verbs: ["get"]

---

kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
rules:
  # Allow Metrics Scraper to get metrics from the Metrics server
  - apiGroups: ["metrics.k8s.io"]
    resources: ["pods", "nodes"]
    verbs: ["get", "list", "watch"]

---

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: kubernetes-dashboard
subjects:
  - kind: ServiceAccount
    name: kubernetes-dashboard
    namespace: kubernetes-dashboard

---

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: kubernetes-dashboard
subjects:
  - kind: ServiceAccount
    name: kubernetes-dashboard
    namespace: kubernetes-dashboard

---

kind: Deployment
apiVersion: apps/v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      k8s-app: kubernetes-dashboard
  template:
    metadata:
      labels:
        k8s-app: kubernetes-dashboard
    spec:
      containers:
        - name: kubernetes-dashboard
          image: kubernetesui/dashboard:v2.1.0
          imagePullPolicy: Always
          ports:
            - containerPort: 8443
              protocol: TCP
          args:
            - --auto-generate-certificates
            - --namespace=kubernetes-dashboard
            - --tls-key-file=apiserver.key
            - --tls-cert-file=apiserver.crt
            # 上面2行我加的 by sskcal
            # Uncomment the following line to manually specify Kubernetes API server Host
            # If not specified, Dashboard will attempt to auto discover the API server and connect
            # to it. Uncomment only if the default does not work.
            # - --apiserver-host=http://my-address:port
          volumeMounts:
            - name: kubernetes-dashboard-certs
              mountPath: /certs
              # Create on-disk volume to store exec logs
            - mountPath: /tmp
              name: tmp-volume
          livenessProbe:
            httpGet:
              scheme: HTTPS
              path: /
              port: 8443
            initialDelaySeconds: 30
            timeoutSeconds: 30
          securityContext:
            allowPrivilegeEscalation: false
            readOnlyRootFilesystem: true
            runAsUser: 1001
            runAsGroup: 2001
      volumes:
        - name: kubernetes-dashboard-certs
          secret:
            secretName: kubernetes-dashboard-certs
        - name: tmp-volume
          emptyDir: {}
      serviceAccountName: kubernetes-dashboard
      nodeSelector:
        "kubernetes.io/os": linux
      # Comment the following tolerations if Dashboard must not be deployed on master
      tolerations:
        - key: node-role.kubernetes.io/master
          effect: NoSchedule

---

kind: Service
apiVersion: v1
metadata:
  labels:
    k8s-app: dashboard-metrics-scraper
  name: dashboard-metrics-scraper
  namespace: kubernetes-dashboard
spec:
  ports:
    - port: 8000
      targetPort: 8000
  selector:
    k8s-app: dashboard-metrics-scraper

---

kind: Deployment
apiVersion: apps/v1
metadata:
  labels:
    k8s-app: dashboard-metrics-scraper
  name: dashboard-metrics-scraper
  namespace: kubernetes-dashboard
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      k8s-app: dashboard-metrics-scraper
  template:
    metadata:
      labels:
        k8s-app: dashboard-metrics-scraper
      annotations:
        seccomp.security.alpha.kubernetes.io/pod: 'runtime/default'
    spec:
      containers:
        - name: dashboard-metrics-scraper
          image: kubernetesui/metrics-scraper:v1.0.6
          ports:
            - containerPort: 8000
              protocol: TCP
          livenessProbe:
            httpGet:
              scheme: HTTP
              path: /
              port: 8000
            initialDelaySeconds: 30
            timeoutSeconds: 30
          volumeMounts:
          - mountPath: /tmp
            name: tmp-volume
          securityContext:
            allowPrivilegeEscalation: false
            readOnlyRootFilesystem: true
            runAsUser: 1001
            runAsGroup: 2001
      serviceAccountName: kubernetes-dashboard
      nodeSelector:
        "kubernetes.io/os": linux
      # Comment the following tolerations if Dashboard must not be deployed on master
      tolerations:
        - key: node-role.kubernetes.io/master
          effect: NoSchedule
      volumes:
        - name: tmp-volume
          emptyDir: {}
```

## 登录Dashboard

### 1. 创建服务账户

```bash
kubectl apply -f ./dashboard-adminuser.yaml
```

```yaml
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

### 2. 创建一个ClusterRoleBinding

```bash
kubectl apply -f ./dashboard-ClusterRoleBinding.yaml
```

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

### 3. 获取登录Token

```bash
#获取token
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
```
