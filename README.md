# K8s
Kubernetes is an orchestration tool for managing distributed services or containerized applications across a distributed cluster of nodes. It was designed for natively supporting (auto-)scaling, high availability, security and portability. Kubernetes itself follows a client-server architecture, with a master node composed of etcd cluster, kube-apiserver, kube-controller-manager, cloud-controller-manager, scheduler. Client (worker) nodes are composed of kube-proxy and kubelet components. Core concepts in Kubernetes include pods (a group of containers deployed together), services (a group of logical pods with a stable IP address) and deployments (a definition of the desired state for a pod or replica set, acted upon by a controller if the current state differs from the desired state), among others.

Info about Kubernetes
Kubernetes in the most powerfull container orchestration engine
Its an open sorce project and is free for everyone

Developement info
Developed by Google and CNCF
7 June 2014 is the Release date
written in Go lang
Kubernetes multinode setup
we have 4 machine , 1 master and 3 worker
Pre-requisite
Disable selinux in all the nodes
  [root@master ~]# setenforce  0
  [root@master ~]# sed -i 's/SELINUX=enforcing/SELINUX=disabled/'  /etc/selinux/config
  
Enable the kernel bridge for every system

[root@master ~]# modprobe br_netfilter
[root@master ~]# echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
Disable the swap

[root@master ~]# swapoff  -a
Installing docker and kubeadm in all the nodes
[root@master ~]# yum  install  docker kubeadm  -y
if kubeadm is not present in your repo
you can browse this link kubernetes repo

yum can be configure by running this command
cat  <<EOF  >/etc/yum.repos.d/kube.repo
[kube]
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
gpgcheck=0
EOF
Start service of docker & kubelet in all the nodes
[root@master ~]# systemctl enable --now  docker kubelet
Do this only on Kubernetes Master
We are here using Calico Networking so we need to pass some parameter you can start Kubernetes_networking from this

[root@master ~]# kubeadm  init --pod-network-cidr=192.168.0.0/16
this is optional
In case of cloud like aws , azure if want to bind public with certificate of kubernetes

[root@master ~]# kubeadm init --pod-network-cidr=192.168.0.0/16 --apiserver-advertise-address=0.0.0.0   --apiserver-cert-extra-sans=publicip,privateip,serviceip
Use the output of above command and paste it to all the worker nodes
Do this step in master node
[root@master ~]# mkdir -p $HOME/.kube
[root@master ~]#  cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
[root@master ~]# chown $(id -u):$(id -g) $HOME/.kube/config
Now apply calico project
kubectl apply -f https://docs.projectcalico.org/v3.8/manifests/calico.yaml
After this all nodes will be ready in state

Now you can check nodes status

[root@master ~]# kubectl get nodes
NAME                 STATUS   ROLES    AGE     VERSION
master.example.com   Ready    master   11m     v1.12.2
node1.example.com    Ready    <none>   9m51s   v1.12.2
node2.example.com    Ready    <none>   9m25s   v1.12.2
node3.example.com    Ready    <none>   9m3s    v1.12.2

Good luck !!
