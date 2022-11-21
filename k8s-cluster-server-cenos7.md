# 1. Info
```sh
 Master:

 Worker01:

 Woker02:
```
# 2. Install Docker
https://docs.docker.com/engine/install/centos/
# 3. Install K8S
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/


```bash
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF

# Set SELinux in permissive mode (effectively disabling it)
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

sudo yum install -y kubeadm-1.23.0 kubelet-1.23.0 kubectl-1.23.0 --disableexcludes=kubernetes

sudo systemctl enable --now kubelet
```
- set iptable
vi /etc/sysctl.conf,add new 2 line.
```
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
```

```
sysctl --system
```
-turn off swap
```
sudo sed -i '/swap/d' /etc/fstab
sudo swapoff -a
```
# 4. install master node
note:  install for server master
- restart kubeadm
``` bash
 sudo kubeadm reset
```
- create private network for cluster
```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

```bash
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

```
- install calicao
```bash
kubectl apply -f https://docs.projectcalico.org/v3.20/manifests/calico.yaml
```
GET JOIN CLUSTER FOR WORKER NODE IF YOU FORGET. Run in server master:
```BASH
kubeadm token create --print-join-command
```
join cluster for worker:
```
sudo kubeadm join host:port --token token-example --discovery-token-ca-cert-hash sha256:key_example
```
get node in cluster k8s:
```
kubectl get nodes
```
# 5. Join worker to master.
```
sudo kubeadm join host:port --token token-example --discovery-token-ca-cert-hash sha256:key_example
```

# NOTE:
	- tutorial for beginer
	- if you have many server, you should install with k3s or kubespray.
copyright by kiu.kiu.duck.pea
