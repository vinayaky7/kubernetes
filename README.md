# Please follow below links to configure Kubernetes cluster on AWS EKS(Elastic Kubernetes Service)

https://docs.aws.amazon.com/eks/latest/userguide/getting-started-console.html

# Cluster Service role

https://docs.aws.amazon.com/eks/latest/userguide/service_IAM_role.html

# NodeIAMRole

https://docs.aws.amazon.com/eks/latest/userguide/create-node-role.html

## Persistent Storage

https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html

## Dashboard:-

https://docs.aws.amazon.com/eks/latest/userguide/dashboard-tutorial.html


# Installation & Configuration of kubernetes Cluster manually on EC2

## We have created 1 Master Node & 2 Worker Node on AWS EC2 with RHEL 8 OS

### Requirement:-

**2GB RAM 2 CPU

Full Network Network Connectivity

DNS Server**


We will be installing Kubernetes Cluster with **Kubdeadm**

This project is created for learning Kubernetes during COVID PANDEMIC

Steps to install kubelet kubeadm kubectl on RHEL 8

Note:- Please follow below link & open all the ports in your Firewall.

https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

yum install -y yum-utils 

yum install -y device-mapper-persistent-data lvm2

yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

or

dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo

**Update the docker-ce repo file**

https://forums.docker.com/t/docker-ce-stable-x86-64-repo-not-available-https-error-404-not-found-https-download-docker-com-linux-centos-7server-x86-64-stable-repodata-repomd-xml/98965/5

yum makecache 

# Install the latest version of Docker CE on RHEL:

yum -y install docker-ce

Error: -
Error:
 Problem: package docker-ce-3:19.03.8-3.el7.x86_64 requires containerd.io >= 1.2.2-3, but none of the providers can be installed
  - cannot install the best candidate for the job
  - package containerd.io-1.2.10-3.2.el7.x86_64 is excluded
  - package containerd.io-1.2.13-3.1.el7.x86_64 is excluded
  - package containerd.io-1.2.2-3.3.el7.x86_64 is excluded
  - package containerd.io-1.2.2-3.el7.x86_64 is excluded
  - package containerd.io-1.2.4-3.1.el7.x86_64 is excluded
  - package containerd.io-1.2.5-3.1.el7.x86_64 is excluded
  - package containerd.io-1.2.6-3.3.el7.x86_64 is excluded
(try to add '--skip-broken' to skip uninstallable packages or '--nobest' to use not only best candidate packages)

Solution:-

yum install -y https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm

Reference Link:-

https://vexpose.blog/2020/04/02/installation-of-docker-fails-on-centos-8-with-error-package-containerd-io-1-2-10-3-2-el7-x86-64-is-excluded

https://www.tecmint.com/install-a-kubernetes-cluster-on-centos-8

yum -y install docker-ce

systemctl start docker

docker -v

vi /etc/yum.repos.d/kubernetes.repo

===========================================

[kubernetes]

name=Kubernetes

baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64

enabled=1

gpgcheck=1

repo_gpgcheck=1

gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg


==================================

yum makecache -y

yum update -y

yum install -y kubelet kubeadm kubectl 

or

dnf install  kubelet kubeadm kubectl

source <(kubectl completion bash)

kubectl completion bash > /etc/bash_completion.d/kubectl

kubeadm config images pull

This will install apps as per below latest version available at the time of installation.
- Docker version 19.03.11, build 42e35e61f3
- kubeadm (1.9.0-00).
- kubectl (1.9.0-00).
- kubelet (1.9.0-00).
- kubernetes-cni (0.6.0-00).
- 
Follow above steps on all VMs irrespective of their status as ‘Nodes’ or ‘Master’.

===================================================

To initialize kubetnetes cluster run below command

$ kubeadm init

Or

kubeadm init --ignore-preflight-errors=all

if you have multiple network interfaces & use any one of them speciically, then specify IP of the interface & fire below command

$ kubeadm init --apiserver-advertise-address 192.168.1.8 --ignore-preflight-errors=all

IP address in above command is the ip address of Kubemaster master of one of the interfaces that you wish to use & server that you want to advertise

This will download and initiate kuberadm container and other apps 

You will below message

=====================

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user

  mkdir -p $HOME/.kube
  
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.

Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:

https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root

kubeadm join 172.31.44.132:6443 --token nvycip.6cvxedsr3r3n9x1x --discovery-token-ca-cert-hash sha256:0707864a192e9f4a5773fccdc9514c1ed48c169b13a198abe346cc7a9c0981b4
    
=================

We will need to install third party Network connector for Kubernetes Networking

We will use below command to start network router service (POD) 

$ kubectl apply --filename https://git.io/weave-kube-1.6

Or

$ kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"

Above command will initialize pod networking for kubernetes cluster.

Now on the VMs that are going to be the K8S nodes, run below command on the node that we want to add

$ kubeadm join --token fc3846.48223acdabb2ae31 <master-ipaddress>:6443 --discovery-token-ca-cert-hash sha256:a905fc15814645e8a1cb3a18234feea5206263db820889d0b773cc1cd7751a0e

Also we can list all available and valid tokens using command in the Master

$ kubeadm token list

Once the pods are joined we can check the same on the Kubernetes master

root@kube-master:# kubectl get nodes

-NAME STATUS ROLES AGE VERSION

-kube-master Ready master 21m v1.9.0

-kubenode-1 Ready <none> 2m v1.9.0

-kubenode-2 Ready <none> 39s v1.9.0

### If you want to create single node cluster with master & worker nodes in one then follow below comand 

OR

### In order to create PODs on kubernetes master in absence of worker nodes, use can use K8s master to do so with below command

$ kubectl taint nodes --all node-role.kubernetes.io/master-

To run any *.yaml file please follow below command

kubectl apply -f *.yaml

Perisistent Storage:-

https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/

kubectl apply -f pv-volume.yaml

kubectl get pv task-pv-volume

kubectl apply -f pv-claim.yaml

kubectl get pv task-pv-volume

kubectl apply -f static-web.yaml

kubectl get pod task-pv-pod





