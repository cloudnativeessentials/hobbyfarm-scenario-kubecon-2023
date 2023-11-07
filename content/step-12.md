+++
title = "Kubernetes Installation: Create a Cluster with kubeadm"
weight = 12
+++

# Create a cluster with kubeadm

1. Do a dryrun with kubeadm to create a cluster

```ctr:kubernetes
sudo kubeadm init --cri-socket=unix:///run/containerd/containerd.sock --dry-run && echo "Dry-Run OK"
```

2. If the dryrun is successful, continue with creating a cluster

```ctr:kubernetes
sudo kubeadm init --cri-socket=unix:///run/containerd/containerd.sock
```

Expected output should include:

```shell
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.42.53:6443 --token 0cy7s2.w9tjwwg2qdryn4aa \
	--discovery-token-ca-cert-hash sha256:b3b8cce7e14bab053afd42e4c5260370c90e86ccb6602a9c45f1b6c4f5d9d103 
```

2. Follow the instructions from the end of `kubeadm init`, this copies the kubeconfig file to where kubectl expectes the kubeconfig file to be.
The kubeconfig file contains how you will authenticate to the kubernetes cluster.

```ctr:kubernetes
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

3. Test kubectl can retrieve the cluster info

```ctr:kubernetes
kubectl cluster-info
```

Expected output:

```shell
Kubernetes control plane is running at https://172.31.42.53:6443
CoreDNS is running at https://172.31.42.53:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

4. By default, control plane nodes are tainted
Remove the taint on the node

```ctr:kubernetes
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```

Expected output:

```shell
node/${vminfo:kubernetes:hostname} untainted
```

5. Check if the cluster is ready

```ctr:kubernetes
kubectl get nodes
```

Expected output:

```shell
NAME              STATUS     ROLES           AGE   VERSION
${vminfo:kubernetes:hostname}   NotReady   control-plane   12m   v1.28.2
````


