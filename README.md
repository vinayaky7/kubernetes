# kubernetes-2020

This project is created for learning Kubernetes in 2020

Steps to install kubelet kubeadm kubectl on RHEL 8

yum install -y yum-utils 

yum install -y device-mapper-persistent-data lvm2

yum-config-manager --enable rhel-8-server-extras-rpms

yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

or

dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo

yum makecache fast or yum makecache 

# Install the latest version of Docker CE on RHEL:

yum -y install docker-ce

#Start Docker:
systemctl start docker

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

yum install -y https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm

Reference Link:-

https://vexpose.blog/2020/04/02/installation-of-docker-fails-on-centos-8-with-error-package-containerd-io-1-2-10-3-2-el7-x86-64-is-excluded

https://www.tecmint.com/install-a-kubernetes-cluster-on-centos-8

cat <<EOF > /etc/yum.repos.d/kubernetes.repo

[kubernetes]

name=Kubernetes

baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64

enabled=1

gpgcheck=1

repo_gpgcheck=1

gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg

https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg

EOF

yum makecache

yum update

yum install -y kubelet kubeadm kubectl –y

source <(kubectl completion bash)

kubectl completion bash > /etc/bash_completion.d/kubectl

kubeadm config images pull

This will install apps as per below latest version available at the time of installation.
- docker.io (1.13.1-0ubuntu1~16.04.2).
- kubeadm (1.9.0-00).
- kubectl (1.9.0-00).
- kubelet (1.9.0-00).
- kubernetes-cni (0.6.0-00).
- 
Follow above steps on all VMs irrespective of their status as ‘Nodes’ or ‘Master’.

To create a kube cluster Run

$ kubeadm init
Or
if you have multiple Kubeadm master then specify IP o the master & fire below command

$ kubeadm init --apiserver-advertise-address 192.168.1.8 --ignore-preflight-errors=all

IP address in above command is the ip address of Kubemaster server that you want to advertise
This will download and initiate kuberadm container and other apps 

You will below message

=====================
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.44.132:6443 --token nvycip.6cvxedsr3r3n9x1x \
    --discovery-token-ca-cert-hash sha256:0707864a192e9f4a5773fccdc9514c1ed48c169b13a198abe346cc7a9c0981b4
=================

We use the command to start network router service (POD) using below command
$ kubectl apply --filename https://git.io/weave-kube-1.6

Or

$ kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"

Now on the VMs that are going to be the K8S nodes, run below command on the node that we want to add..
$ kubeadm join --token fc3846.48223acdabb2ae31 <master-ipaddress>:6443 --discovery-token-ca-cert-hash sha256:a905fc15814645e8a1cb3a18234feea5206263db820889d0b773cc1cd7751a0e

If the Kubeadm join command is not available, we can create anew token it with below command
$ sudo kubeadm token create --print-join-command

Or, below can retrieve existing token
$ kubeadm join --discovery-token-unsafe-skip-ca-verification --token=`kubeadm token list` 172.17.0.92:6443

Also we can list all available and valid tokens using command
$ kubeadm token list

Once the pods are joined we can check the same on the Kubernetes master

root@kube-master:#kubectl get nodes
NAME STATUS ROLES AGE VERSION
kube-master Ready master 21m v1.9.0
kubenode-1 Ready <none> 2m v1.9.0
kubenode-2 Ready <none> 39s v1.9.0

If you want to create single node cluster with master & worker nides in one then follow below comand 

In order to allow PODs to be created on kubernetes master, use below command
$ kubectl taint nodes --all node-role.kubernetes.io/master-




