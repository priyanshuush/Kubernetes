# Create Kubernetes cluster in CentOS

## System Requirments
|Role|FQDN|OS|RAM|CPU|
|----|----|----|----|----|
|Master|kmaster.example.com|CentOS 7|2G|2|
|Worker|kworker.example.com|CentOS 7|1G|1|

## On both Kmaster & Kworker
As root 
#### Disable Firewall
```
systemctl disable firewalld
systemctl stop firewalld
```
##### Disable swap
```
swapoff -a; sed -i '/swap/d' /etc/fstab
```
#### Disable SELinux
```
setenforce 0
sed -i --follow-symlinks 's/^SELINUX=enforcing/SELINUX=disabled/' /etc/sysconfig/selinux
```
#### Install Container Runtime (Docker)
```
yum install docker.io -y
systemctl enable docker
systemctl start docker
```

##### Update sysctl settings for Kubernetes networking
```
cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```

### Kubernetes Setup
##### Add yum repository
```
cat >>/etc/yum.repos.d/kubernetes.repo<<EOF
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
        https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
```
#### Install K8s Components
```
yum install -y kubeadm-1.18.5-0 kubelet-1.18.5-0 kubectl-1.18.5-0
```
#### Enable and start kubelet service
```
systemctl eneble --now kubelet
```

## On Master Node(Kmaster)
##### Initialize Kubernetes Cluster
```
kubeadm init --apiserver-advertise-address=`masteNode IP address` --pod-network-cidr=192.168.0.0/16
```
##### Deploy Calico Network
```
kubectl --kubeconfig=/etc/kubernetes/admin.conf create -f https://docs.projectcalico.org/v3.14/manifests/calico.yaml
```
##### Cluster Join Command
``` 
kubeadm token create --print-join-command
```
##### To be able to run kubectl commands as non-root user
If you want to be able to run kubectl commands as non-root user, then as a non-root user perform these
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

```


## On Worker Node(Kworker)
#### join the Cluster
paste the Output(Token) from __kubeadm token create__ command in previous step from the master server and run here.

## Verifying the cluster
##### Get Nodes Status
```
kubectl get nodes
```
##### Get Component status
```
kubectl get cs
```
