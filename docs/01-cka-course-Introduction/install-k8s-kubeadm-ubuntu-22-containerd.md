# Install Kubernetes Cluster using kubeadm
Follow this documentation to set up a Kubernetes cluster on __Ubuntu 22.04 LTS__.

## Software and OS used here:

#### OS      :  ubuntu 22.04
#### kubeadm :  1.28.0-00
#### kubelet :  1.28.0-00
#### kubectl :  1.28.0-00

This documentation guides you in setting up a cluster with one master node and one worker node.

## Assumptions

1. You have 4 physical or virutal machine for create kubernetes cluster.
2. You have to assigned password of each node.
3. Each node have to assign the corresponding hostname.

|Role|FQDN|IP|OS|RAM|CPU|
|----|----|----|----|----|----|
|Master|k8s-master.kloudbytes.com  |172.16.0.100|Ubuntu 22.04|2G|2|
|Worker|k8s-worker1.kloudbytes.com |172.16.0.101|Ubuntu 22.04|1G|1|
|Worker|k8s-worker2.kloudbytes.com|172.16.0.102|Ubuntu 22.04|1G|1|
|Worker|k8s-worker3.kloudbytes.com |172.16.0.103|Ubuntu 22.04|1G|1|

## On both Kmaster and Kworker
##### Login as `root` user
```
sudo su -
```
Perform all the commands as root user unless otherwise specified
##### Stop and Disable firewall
```
ufw disable
```
#### Enable and Load Kernel modules
```
cat >>/etc/modules-load.d/containerd.conf<<EOF
overlay
br_netfilter
EOF
modprobe overlay
modprobe br_netfilter
```
##### Disable and turn off SWAP
```
sed -i '/swap/d' /etc/fstab ; swapoff -a
```
##### Update sysctl settings for Kubernetes networking
```
cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
EOF
sysctl --system
```
##### Install containerd runtime
```
apt update
apt install -qq -y ca-certificates curl gnupg lsb-release
mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
apt update 
apt install
containerd config default > /etc/containerd/config.toml
sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
systemctl restart containerd
systemctl enable containerd >/dev/null
```
#### Add apt repo for kubernetes

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add - 
apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
```
#### Install Kubernetes components (kubeadm, kubelet and kubectl)
```
apt install -qq -y kubeadm=1.28.0-00 kubelet=1.28.0-00 kubectl=1.28.0-00 
```
#### Install net-tools components (ifconfig )
```
apt install -qq -y net-tools >/dev/null 2>&1
```
#### Enable ssh password authentication
```
sed -i 's/^PasswordAuthentication .*/PasswordAuthentication yes/' /etc/ssh/sshd_config
echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config
systemctl reload sshd
```
#### Update /etc/hosts file
```
cat >>/etc/hosts<<EOF
172.16.0.100   k8s-master.kloudbytes.com     master 
172.16.0.101   k8s-worker1.kloudbytes.com    worker1 
172.16.0.102   k8s-worker2.kloudbytes.com    worker2
172.16.0.103   k8s-worker3.kloudbytes.com    worker3 
EOF
```
## Kubernetes Setup On k8s-master

#### Pull required containers
```
kubeadm config images pull
```
### Initialize Kubernetes Cluster
Note: apiserver-advertise-address is your k8s-master ip address
```
kubeadm init --apiserver-advertise-address=172.16.0.100 --pod-network-cidr=192.168.0.0/16 
```
##### Deploy Calico network
```
kubectl --kubeconfig=/etc/kubernetes/admin.conf create -f https://docs.projectcalico.org/v3.18/manifests/calico.yaml
```

##### Cluster join command

```
kubeadm token create --print-join-command > /print-join-cluster.txt
```
Note: use "print-join-cluster.txt file for future use to join worker node into k8s-master.

##### To be able to run kubectl commands as non-root user
If you want to be able to run kubectl commands as non-root user, then as a non-root user perform these

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## On k8s-worker<*>
##### Join the worker node into k8s-worker<*>

Use the output from __kubeadm token create__ command in previous step from the master server and run here.

```
Sample format:
kubeadm join 172.16.0.100:6443 --token hp9b0k.1g9tqz8vkf4s5h278ucwf  --discovery-token-ca-cert-hash sha256:32eb67948d72ba99aac9b5bb0305d66a48f43b0798cb2df99c8b1c30708bdc2cased24sf
```

## Verifying the cluster (On k8s-master)
##### Get kubernetes Cluster Nodes status
```
kubectl get nodes
```
or 
```
kubectl get no
kubectl get node
kubectl get nodes
```
Above commands will give same output.
```
kubectl get node -o wide
```
Note: with node wide optipn

##### Get component status
```
kubectl get cs
```

# "Be a lifelong student. The more you learn, the more you earn and the more self-confidence you will have." – Brian Tracy

## Have a fun... :grinning:
