## How to install Kubernetes Cluster in Centos 7 Step by Step Guide

### First Step to Add Firewall rules if your firewalld is running (You can skip if not running)
```
firewall-cmd --permanent --add-port=6443/tcp
firewall-cmd --permanent --add-port=2379-2380/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --permanent --add-port=10251/tcp
firewall-cmd --permanent --add-port=10252/tcp
firewall-cmd --permanent --add-port=10255/tcp
firewall-cmd –reload
modprobe br_netfilter
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
```

### Remove Older Docker if already installed
```
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine 
```

### Install yum utilities
```	
sudo yum install -y yum-utils
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
systemctl enable docker && systemctl start docker

sudo rm /etc/containerd/config.toml
sudo systemctl restart containerd
```	


### You can skip if Containerd started successfully
```	
[root@kubemaster ~]#  rm /etc/containerd/config.toml
rm: cannot remove ‘/etc/containerd/config.toml’: No such file or directory
[root@kubemaster ~]# systemctl restart containerd
```	

### Create YUM repository to install required packages
```	
vi /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
```	

### Install Kubeadm and Docker packages

```	
yum install kubeadm docker -y 

systemctl enable kubelet && systemctl start kubelet
```	

### Example Command 
```	
kubeadm join 192.168.117.200:6443 --token 869h67.4x0irxa14419ep1s \
        --discovery-token-ca-cert-hash sha256:da5ed4a9ec779f8b1eca198e8d6fe7b5cf8c29fa8f1f512b0099a808b17759bc
```	

### Add Kube Flannel to create Networking between all the Nodes
```	
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```	