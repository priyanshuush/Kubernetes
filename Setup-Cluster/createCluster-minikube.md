# Create Single Node Cluster using minikube in Ubuntu

#### First install kubectl
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```
#### Install minikube
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```
#### Start minikube cluster(single node)
```
minikube start
```
#### To access minikube dashboard
```
minikube addons enable dashboard
minikuke dashboard --url
```
* To open dashboard of minikube cluster in your machine running in remote vm: ` ssh -i -/.ssh/(your ssh key) -L 8081:localhost:(port mentioned in url) ec2-user@yourIPAddress `

* To use kubectl insted of minnikube kubectl write: `alias kubectl="minikube kubectl --" `
