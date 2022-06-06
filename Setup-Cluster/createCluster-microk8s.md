# Create Single Node Cluster using microk8s in Ubuntu

### Run these Command as root
```
sudo su
```

* Run this command to Install snapd package: `apt-get install snapd`

* Command to install microk8s: `snap install microk8s --classic`


#### Basic microk8s Commands

##### To get cluster info
```
microk8s.kubectl cluster-info
```
##### To use __kubectl__ instead of __microk8s.kubectl__
```
alias kubectl='microk8s.kubectl'
```
##### To see all Namespaces
```
kubectl get namespaces
```
##### Create nginx deployment in cluster
```
kubectl run nginx --image
```
* Cheak all the components running:`microk8s.kubectl get all -o wide` 

##### To increase number of replicas in above deployment
```
kubectl scale deploy nginx --replicas=2
```
##### Expose the above deployment 
```
kubectl expose deploy nginx --port 80 --target-port 80 --type ClusterIP
```
##### Remove the components all at once
```
microk8s.reset
```
