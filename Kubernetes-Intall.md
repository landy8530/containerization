# Kubernetes 安装

## 1 Docker for Mac Desktop集成（学习使用）

### 1.1 Enable Kubernetes![Deployment evolution](./images/k8s-docker-4-mac.png)

### 1.2 验证

```
➜  tools kubectl get nodes
NAME             STATUS   ROLES                  AGE   VERSION
docker-desktop   Ready    control-plane,master   24s   v1.21.4
➜  tools kubectl cluster-info
Kubernetes control plane is running at https://kubernetes.docker.internal:6443
CoreDNS is running at https://kubernetes.docker.internal:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
➜  tools kubectl describe node
Name:               docker-desktop
Roles:              control-plane,master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=docker-desktop
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node-role.kubernetes.io/master=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
.......
```

![Deployment evolution](./images/k8s-docker-4-mac-status.png)



## 2 二进制安装部署（生产环境适用，新手推荐）

使用二进制安装部署K8S的要点：

- 基础设置环境准备好
  - CentOS7.6系统（内核在3.8.x以上）
  - 关闭SELinux，关闭firewalld服务
  - 时间同步（chronyd）
  - 调整Base源，Epel源
  - 内核优化（文件描述符大小，内核转发，等等）
- 安装部署bind9内网DNS系统
- 安装部署docker的私有仓库-harbor
- 准备证书签发环境-cfssl（很多K8S的新特性都是需要SSL通信的）
  - cfssl：证书签发的主要工具
  - cfssl-json：将cfssl生成的证书（json格式）变为文件承载式证书
  - cfssl-certinfo：验证证书的信息
- 安装部署主控节点服务（4个）
  - Etcd（可以单独部署在一台机器上，建议部署奇数台机子，这是由于其选举机制决定的）
  - Apiserver
  - Controller-manager
  - Scheduler
- 安装部署运算节点服务（2个）
  - Kubelet
  - Kebe-proxy

> 主控节点和运算节点只是逻辑上的区分，实际上也可以在同一台机子上。
>
> 关于kubeconfig文件：
>
> - 这是一个K8S用户的配置文件
> - 它里面包含证书信息
> - 证书过期或更换，需要同步替换该文件

## 3 使用Kubeadmin进行部署（相对简单，熟手推荐）

## 4 部署Kubernetes Dashboard

https://github.com/kubernetes/dashboard

### 4.1 启动Dashboard

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/aio/deploy/recommended.yaml
# 开启本机访问代理，默认监听 8001 端口
$ kubectl proxy
```

### 4.2 登录

访问以下地址，

`http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login`

![Deployment evolution](./images/k8s-dashboard-login-page.png)

### 4.3 创建管理员用户

首先创建文件dashboard-admin.yaml:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: kubernetes-dashboard-admin
  namespace: kubernetes-dashboard
  labels:
    k8s-app: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard-admin
  namespace: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard-admin
  namespace: kubernetes-dashboard
```

然后创建管理员用户，生成 Token

```
# 创建 ServiceAccount kubernetes-dashboard-admin 并绑定集群管理员权限
$ kubectl apply -f dashboard-admin.yaml

# 获取登陆 token
$ kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep kubernetes-dashboard-admin | awk '{print $1}')
```

Token生成如下：

![Deployment evolution](./images/k8s-dashboard-token.png)

### 4.4 使用Token登陆

填入上面的Token后即可登录，

![Deployment evolution](./images/k8s-dashboard.png)

