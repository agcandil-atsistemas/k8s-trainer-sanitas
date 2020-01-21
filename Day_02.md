# Day 2

## [Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## [Kubernetes - Installing with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

```[bash]
vagrant ssh master1
sudo su -
apt-get update -y
```

```[bash]
# ensure legacy binaries are installed
sudo apt-get install -y iptables arptables ebtables

# switch to legacy versions
sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
sudo update-alternatives --set arptables /usr/sbin/arptables-legacy
sudo update-alternatives --set ebtables /usr/sbin/ebtables-legacy
```

### Install containerd + cri-utils

```[bash]
cat > /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF

modprobe overlay
modprobe br_netfilter

# Setup required sysctl params, these persist across reboots.
cat > /etc/sysctl.d/99-kubernetes-cri.conf <<EOF
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

sysctl --system


sudo sh -c "curl -LSs https://storage.googleapis.com/cri-containerd-release/cri-containerd-cni-1.3.2.linux-amd64.tar.gz |tar --no-overwrite-dir -C / -xz"
crictl info

sudo systemctl enable --now containerd
crictl info
```

### Install kubelet kubeadm kubectl

```[bash]
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

### corregir ips

```[bash]

cat <<EOF >  /etc/default/kubelet
KUBELET_EXTRA_ARGS=--node-ip=192.168.88.11
EOF

cat <<EOF >  /etc/default/kubelet
KUBELET_EXTRA_ARGS=--node-ip=192.168.88.31
EOF

cat <<EOF >  /etc/default/kubelet
KUBELET_EXTRA_ARGS=--node-ip=192.168.88.32
EOF
``` 

### kubeadm init (master)

```[bash]
sudo kubeadm init --apiserver-advertise-address=192.168.88.11 --pod-network-cidr=10.244.0.0/16 --cri-socket /run/containerd/containerd.sock 
```

### kubeadm join (node)

```[bash]
kubeadm join 192.168.88.11:6443 --token qvmyxp.ty0snqyhfmbvlssd \
    --discovery-token-ca-cert-hash sha256:4ae740ec0ab0d6206efbd4917eb9d5e7cfdb3139ec2b7b6882b6ac0d60dfc725 
```

### [Token management](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-token/)

## [Install CNI: flannel](https://kubernetes.io/docs/concepts/cluster-administration/networking/)

```[bash] 
kubectl apply -f https://raw.githubusercontent.com/agcandil-atsistemas/k8s-trainer-sanitas/master/flannel/kube-flannel.yml
```

## [Install Web UI (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)

Deploy and access the Dashboard web user interface to help you manage and monitor containerized applications in a Kubernetes cluster.

```[bash]

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml

kubectl proxy
```

[Link Console UI](http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/)

To get Bearer Token:

```[bash]
$ kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | awk '/^deployment-controller-token-/{print $1}') | awk '$1=="token:"{print $2}'
```

### [Create Sample User](https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md)

```[bash]
kubectl apply -f dashboard/

kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')

```
## Using the kubectl Command-line

Install and setup the kubectl command-line tool used to directly manage Kubernetes clusters.

```[bash]
$ kubectl get nodes
NAME       STATUS   ROLES    AGE   VERSION
minikube   Ready    master   23d   v1.16.2

$ kubectl get pods 
No resources found in default namespace.

$ kubectl get pods --all-namespaces
NAMESPACE              NAME                                         READY   STATUS    RESTARTS   AGE
kube-system            coredns-5644d7b6d9-5qbq8                     1/1     Running   2          23d
kube-system            coredns-5644d7b6d9-nvqnm                     1/1     Running   2          23d
kube-system            etcd-minikube                                1/1     Running   2          23d
kube-system            kube-addon-manager-minikube                  1/1     Running   2          23d
kube-system            kube-apiserver-minikube                      1/1     Running   1          15d
kube-system            kube-controller-manager-minikube             1/1     Running   1          15d
kube-system            kube-proxy-pq88s                             1/1     Running   1          15d
kube-system            kube-scheduler-minikube                      1/1     Running   1          15d
kube-system            storage-provisioner                          1/1     Running   4          23d
kubernetes-dashboard   dashboard-metrics-scraper-76585494d8-npz5h   1/1     Running   0          48m
kubernetes-dashboard   kubernetes-dashboard-5996555fd8-pg8tb        1/1     Running   0          48m

$ kubectl cluster-info
Kubernetes master is running at https://192.168.99.100:8443
KubeDNS is running at https://192.168.99.100:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

$ kubernetes-official-tasks$ kubectl cluster-info dump > output.log

$ kubectl -h
kubectl controls the Kubernetes cluster manager.

 Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
  create         Create a resource from a file or from stdin.
  expose         Take a replication controller, service, deployment or pod and expose it as a new
Kubernetes Service
  run            Run a particular image on the cluster
  set            Set specific features on objects

Basic Commands (Intermediate):
  explain        Documentation of resources
  get            Display one or many resources
  edit           Edit a resource on the server
  delete         Delete resources by filenames, stdin, resources and names, or by resources and label selector

Deploy Commands:
  rollout        Manage the rollout of a resource
  scale          Set a new size for a Deployment, ReplicaSet, Replication Controller, or Job
  autoscale      Auto-scale a Deployment, ReplicaSet, or ReplicationController

Cluster Management Commands:
  certificate    Modify certificate resources.
  cluster-info   Display cluster info
  top            Display Resource (CPU/Memory/Storage) usage.
  cordon         Mark node as unschedulable
  uncordon       Mark node as schedulable
  drain          Drain node in preparation for maintenance
  taint          Update the taints on one or more nodes

Troubleshooting and Debugging Commands:
  describe       Show details of a specific resource or group of resources
  logs           Print the logs for a container in a pod
  attach         Attach to a running container
  exec           Execute a command in a container
  port-forward   Forward one or more local ports to a pod
  proxy          Run a proxy to the Kubernetes API server
  cp             Copy files and directories to and from containers.
  auth           Inspect authorization

Advanced Commands:
  diff           Diff live version against would-be applied version
  apply          Apply a configuration to a resource by filename or stdin
  patch          Update field(s) of a resource using strategic merge patch
  replace        Replace a resource by filename or stdin
  wait           Experimental: Wait for a specific condition on one or many resources.
  convert        Convert config files between different API versions
  kustomize      Build a kustomization target from a directory or a remote url.

Settings Commands:
  label          Update the labels on a resource
  annotate       Update the annotations on a resource
  completion     Output shell completion code for the specified shell (bash or zsh)

Other Commands:
  api-resources  Print the supported API resources on the server
  api-versions   Print the supported API versions on the server, in the form of "group/version"
  config         Modify kubeconfig files
  plugin         Provides utilities for interacting with plugins.
  version        Print the client and server version information

Usage:
  kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

```

## Manage Kubernetes Objects

### [Declarative Management of Kubernetes Objects Using Configuration Files](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/declarative-config/)

### [Declarative Management of Kubernetes Objects Using Kustomize](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/)

### [Managing Kubernetes Objects Using Imperative Commands](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/imperative-command/)

### [Imperative Management of Kubernetes Objects Using Configuration Files](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/imperative-config/)