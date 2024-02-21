# minikube installation on ubuntu 22.04: 

## What is minikube ? 

Minikube is an open-source tool that facilitates running Kubernetes clusters locally on a single machine. It's particularly useful for development, testing, and learning purposes. Minikube allows developers to experiment with Kubernetes without the need for a full-scale, production-grade cluster. It sets up a single-node Kubernetes cluster within a virtual machine on your local workstation.

## some key features and functionalities of Minikube:

* ### Local Kubernetes Cluster:
    Minikube enables users to run a Kubernetes cluster locally on their workstation, allowing them to develop and test applications that are destined to be deployed on Kubernetes.

* ### Single Node Configuration:
     By default, Minikube sets up a single-node Kubernetes cluster, which is suitable for development purposes. However, it can be configured to run multi-node clusters for more complex testing scenarios.

* ### Integration with Hypervisors:
     Minikube integrates with various hypervisors like VirtualBox, HyperKit, KVM, and others to create the virtual machines that host the Kubernetes cluster.

* ### Support for Kubernetes Features:
    Minikube supports many Kubernetes features, including deployments, services, namespaces, resource management, networking, and storage, allowing developers to test their applications in a Kubernetes-like environment.

* ### Easy Setup and Management:
     Minikube simplifies the setup and management of local Kubernetes clusters by providing a command-line interface (CLI) with commands to start, stop, delete, and configure the cluster.

* ### Cross-platform Compatibility:
     Minikube supports multiple operating systems, including Linux, macOS, and Windows, making it accessible to a wide range of developers.

## Prerequisites: 
- Ubuntu 22.04 along with ssh access
- sudo user with privilege rights or root access
- Stable Internet Connection
  
## 1. Install Docker Dependencies:
Login to your Ubuntu 22.04 system and run the following apt commands to install docker dependencies,

```
sudo apt update
sudo apt install ca-certificates curl gnupg -y
```

## 2. Add Docker official APT Repository:

```
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
chmod a+r /etc/apt/keyrings/docker.gpg
```

Next, add the Docker repository to the system’s package sources,

```
echo \
"deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
"$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

```

## 3. Install Docker on Ubuntu 22.04
Now that the Docker repository is added, update the package index and install Docker:

```
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

Once the docker package is installed, add your local user to docker group so that user can run docker commands without sudo,

```
sudo usermod -aG docker $USER
newgrp docker
```
Note: Make sure logout and login again after adding local user to docker group

Verify the Docker version

```
docker version
```
## 4. Verify and Test Docker Installation
```
docker run hello-world
```

# Minikube System Requirements
- 2 GB RAM or more
- 2 CPU / vCPU or more
- 20 GB free hard disk space or more
- Docker / Virtual Machine Manager – KVM & VirtualBox  ## Here we used Docker

## 1) Apply updates
Apply all updates of existing packages of your system by executing the following apt commands,
```
 sudo apt update -y
 sudo apt upgrade -y
```
Once all the updates are installed then reboot your system once.

```
 sudo reboot
```
## 2) Install Minikube dependencies

Install the following minikube dependencies by running beneath command,

```
sudo apt install -y curl wget apt-transport-https
```

## 3) Download Minikube Binary
Use the following curl command to download latest minikube binary,

```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

Once the binary is downloaded, copy it to the path /usr/local/bin and set the executable permissions on it

```
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

Verify the minikube version

```
minikube version
```
minikube version: v1.30.1
commit: 08896fd1dc362c097c925146c4a0d0dac715ace0

Note: At the time of writing this tutorial, latest version of minikube was v1.30.1.


## 4) Install Kubectl utility
Kubectl is a command line utility which is used to interact with Kubernetes cluster. It is used for managing deployments, service and pods etc. Use below curl command to download latest version of kubectl.

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x kubectl
mv kubectl /usr/local/bin/
```

Now verify the kubectl version

```
kubectl version -o yaml
```

## 5) Start minikube
As we are already stated in the beginning that we would be using docker as base for minikue, so start the minikube with the docker driver, run

  ### NOTE: IF BELOW COMMAND NOT WORKING USE --force
```
minikube start --driver=docker
```

In case you want to start minikube with customize resources and want installer to automatically select the driver then you can run following command,

Example command: Use as per your computer/machine configuraton
```
minikube start --addons=ingress --cpus=2 --cni=flannel --install-addons=true --kubernetes-version=stable --memory=6g
```

Perfect, above confirms that minikube cluster has been configured and started successfully.


Run below minikube command to check status,

```
minikube status
```
Run following kubectl command to verify the Kubernetes version, node status and cluster info.

```
$ kubectl cluster-info
$ kubectl get nodes
```
## 6) Managing Addons on minikube
By default, only couple of addons are enabled during minikube installation, to see the addons of minikube, run the below command.
```
minikube addons list
```
Example:

 ```
minikube addons enable <addon-name>
```

