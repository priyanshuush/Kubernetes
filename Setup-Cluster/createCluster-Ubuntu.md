# Create Kubernetes cluster in Ubuntu


## System Requirments
|Role|FQDN|OS|RAM|CPU|
|----|----|----|----|----|
|Master|kmaster.example.com|Ubuntu 20.04|2G|2|
|Worker|kworker.example.com|Ubuntu 20.04|1G|1|

## On both Kmaster & Kworker
##### As root
```
sudo su
```
#### Disable Firewall
```
ufw disable
```
#### disable swap
```
swapoff -a; sed -i '/swap/d' /etc/fstab
```
#### Update sysctl settings for Kubernetes networking
```
cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```
#### Install Container Runtime(Docker)
```
{
  apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
  add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  apt update
  apt install -y docker-ce=5:19.03.10~3-0~ubuntu-focal containerd.io
}
```
### Kubernetes Setup
###### Add apt repository
```
{
  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
  echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
}
```
##### Install Kubernetes Components
```
apt update && apt install -y kubeadm=1.18.5-00 kubelet=1.18.5-00 kubectl=1.18.5-00
```

## On Master Node(Kmaster)
##### Initialize K8s Cluster
Enter Your master node IP address in `kubeadm init --apiserver-advertise-address=masteNode IP address --pod-network-cidr=192.168.0.0/16  --ignore-preflight-errors=all`
```
kubeadm init --apiserver-advertise-address=masteNode IP address --pod-network-cidr=192.168.0.0/16  --ignore-preflight-errors=all
```
##### Deploy Calico network
```
kubectl --kubeconfig=/etc/kubernetes/admin.conf create -f https://docs.projectcalico.org/v3.14/manifests/calico.yaml
```
##### Cluster join command
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

## Verifying the cluster (On kmaster)
##### Get Nodes status
```
kubectl get nodes
```
##### Get component status
```
kubectl get cs
```
