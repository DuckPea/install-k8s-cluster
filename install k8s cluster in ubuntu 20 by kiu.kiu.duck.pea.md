# install k8s cluster and docker
copyright by kiu.kiu.duck.pea
follow me: github: [kiu.kiu.duck.pea](https://github.com/DuckPea)

note before install:
-  I have 5 server ubuntu 20.04
- i will install k8s version 1.23.6
- containerd user in tutorial: docker 
- k8s's going to support docker in version 1.23.6
# 1. set host name
login your server and set hostname
```bash
sudo hostnamectl set-hostname master
```
```bash
sudo hostnamectl set-hostname worker-1
```
```bash
sudo hostnamectl set-hostname worker-2
```
```bash
sudo hostnamectl set-hostname worker-3
```
```bash
sudo hostnamectl set-hostname worker-4
```
# 2. Install docker
note: install for 5 server
https://docs.docker.com/engine/install/ubuntu/
# 3. intall kubernet
note: install for 5 server
note: you should install old version.
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
``` bash
sudo apt-get install -qy kubeadm=1.23.6-00 kubelet=1.23.6-00 kubectl=1.23.6-00
```
hold
```bash
sudo apt-mark hold kubelet kubeadm kubectl
```
check version
```bash
kubectl version --client && kubeadm version
```
# 4. set config and restart.
note: config for 5 server
 set user mod docker
 ```bash
 sudo usermod -aG docker $USER
    newgrp docker
 ```
set config daemon
```bash
sudo tee /etc/docker/daemon.json  <<EOF
     {
       "exec-opts": ["native.cgroupdriver=systemd"],
       "log-driver": "json-file",
       "log-opts": {
         "max-size": "100m"
       },
       "storage-driver": "overlay2",
       "storage-opts": [
         "overlay2.override_kernel_check=true"
       ]
     }
EOF
```


restart docker
```bash
    sudo systemctl daemon-reload 
    sudo systemctl restart docker
    sudo systemctl enable docker
 ```
# 5. install create and install network for cluster in master.
note:  install for server master
restart kubeadm
``` bash
 sudo kubeadm reset
```
create private network for cluster
```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```
```bash
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

```
install calicao
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
kuberctl get nodes
```
![[Pasted image 20220722165358.png]]
# NOTE:
	- tutorial for beginer
	- if you have many server, you should install with k3s or kubespray.
copyright by kiu.kiu.duck.pea
follow me: github: [kiu.kiu.duck.pea](https://github.com/DuckPea)
